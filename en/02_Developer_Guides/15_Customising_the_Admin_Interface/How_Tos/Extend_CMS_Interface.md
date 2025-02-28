---
title: Extend the CMS interface
summary: Customise the UI of the CMS backend
---

# How to extend the CMS interface

## Introduction

The CMS interface works just like any other part of your website: It consists of
PHP controllers, templates, CSS stylesheets and JavaScript. Because it uses the
same base elements, it is relatively easy to extend.

As an example, we're going to add a permanent "bookmarks" link list to popular pages
into the main CMS menu. A page can be bookmarked by a CMS author through a
simple checkbox.

For a deeper introduction to the inner workings of the CMS, please refer to our
guide on [CMS Architecture](/developer_guides/customising_the_admin_interface/cms_architecture).

## Override a CMS template

If you place a template with an identical name into your application template
directory (usually `app/templates/`), it'll take priority over the built-in
one.

CMS templates are inherited based on their controllers, similar to subclasses of
the common `Page` object (a new PHP class `MyPage` will look for a `MyPage.ss` template).
We can use this to create a different base template with `LeftAndMain.ss`
(which corresponds to the `LeftAndMain` PHP controller class).

Copy the template markup of the base implementation at `templates/SilverStripe/Admin/Includes/LeftAndMain_MenuList.ss`
from the `silverstripe/admin` module
into `app/templates/SilverStripe/Admin/Includes/LeftAndMain_MenuList.ss`. It will automatically be picked up by
the CMS logic. Add a new section into the `<ul class="cms-menu__list">`	

```ss
...
<ul class="cms-menu-list">
    <!-- ... -->
    <li class="bookmarked-link first">
        <a href="$AdminURL('pages/edit/show/1')">Edit "My popular page"</a>
    </li>
    <li class="bookmarked-link last">
        <a href="$AdminURL('pages/edit/show/99')">Edit "My other page"</a>
    </li>
</ul>
...
```

Refresh the CMS interface with `admin/?flush=all`, and you should see those
hardcoded links underneath the left-hand menu. We'll make these dynamic further down.

## Include custom CSS in the CMS

In order to show the links a bit separated from the other menu entries,
we'll add some CSS, and get it to load
with the CMS interface. Paste the following content into a new file called
`app/css/BookmarkedPages.css`:


```css
.bookmarked-link.first {margin-top: 1em;}
```

Load the new CSS file into the CMS, by setting the `LeftAndMain.extra_requirements_css`
[configuration value](../../configuration).


```yml
SilverStripe\Admin\LeftAndMain:
  extra_requirements_css:
    - app/css/BookmarkedPages.css
```

In order to let the frontend have the access to our `css` files, we need to `expose` them in the `composer.json`:

```javascript
    "extra": {
        ...
        "expose": [
            "app/css"
        ]
    },
```

Then run `composer vendor-expose`. This command will publish all the `css` files under the `app/css` folder to their public-facing paths.

> Note: don't forget to `flush`.

## Create a "bookmark" flag on pages

Now we'll define which pages are actually bookmarked, a flag that is stored in
the database. For this we need to decorate the page record with a
`DataExtension`. Create a new file called `app/src/BookmarkedPageExtension.php`
and insert the following code.


```php
use SilverStripe\Forms\CheckboxField;
use SilverStripe\Forms\FieldList;
use SilverStripe\ORM\DataExtension;

class BookmarkedPageExtension extends DataExtension
{

    private static $db = [
        'IsBookmarked' => 'Boolean'
    ];

    public function updateCMSFields(FieldList $fields)
    {
        $fields->addFieldToTab('Root.Main',
            new CheckboxField('IsBookmarked', "Show in CMS bookmarks?")
        );
    }
}

```

Enable the extension in your [configuration file](../../configuration)


```yml
SilverStripe\CMS\Model\SiteTree:
  extensions:
    - BookmarkedPageExtension
```

In order to add the field to the database, run a `dev/build/?flush=all`.
Refresh the CMS, open a page for editing and you should see the new checkbox.

## Retrieve the list of bookmarks from the database

One piece in the puzzle is still missing: How do we get the list of bookmarked
pages from the database into the template we've already created (with hardcoded
links)? Again, we extend a core class: The main CMS controller called
`LeftAndMain`.

Add the following code to a new file `app/src/BookmarkedLeftAndMainExtension.php`;


```php
use SilverStripe\Admin\LeftAndMainExtension;

class BookmarkedPagesLeftAndMainExtension extends LeftAndMainExtension
{

    public function BookmarkedPages()
    {
        return Page::get()->filter("IsBookmarked", 1);
    }
}
```

Enable the extension in your [configuration file](../../configuration)


```yml
SilverStripe\Admin\LeftAndMain:
  extensions:
    - BookmarkedPagesLeftAndMainExtension
```

As the last step, replace the hardcoded links with our list from the database.
Find the `<ul>` you created earlier in `app/templates/SilverStripe/Admin/Includes/LeftAndMain_MenuList.ss`
and replace it with the following:


```ss
<ul class="cms-menu__list">
    <!-- ... -->
    <% loop $BookmarkedPages %>
    <li class="bookmarked-link $FirstLast">
        <li><a href="$AdminURL('pages/edit/show/$ID')">Edit "$Title"</a></li>
    </li>
    <% end_loop %>
</ul>
```

## Extending the CMS actions

CMS actions follow a principle similar to the CMS fields: they are built in the
backend with the help of `FormFields` and `FormActions`, and the frontend is
responsible for applying a consistent styling.

The following conventions apply:

* New actions can be added by redefining `getCMSActions`, or adding an extension
with `updateCMSActions`.
* It is required the actions are contained in a `FieldList` (`getCMSActions`
returns this already).
* Standalone buttons are created by adding a top-level `FormAction` (no such
button is added by default).
* Button groups are created by adding a top-level `CompositeField` with
`FormActions` in it.
* A `MajorActions` button group is already provided as a default.
* Drop ups with additional actions that appear as links are created via a
`TabSet` and `Tabs` with `FormActions` inside.
* A `ActionMenus.MoreOptions` tab is already provided as a default and contains
some minor actions.
* You can override the actions completely by providing your own
`getAllCMSFields`.

Let's walk through a couple of examples of adding new CMS actions in `getCMSActions`.

First of all we can add a regular standalone button anywhere in the set. Here
we are inserting it in the front of all other actions. We could also add a
button group (`CompositeField`) in a similar fashion.


```php
$fields->unshift(FormAction::create('normal', 'Normal button'));
```

We can affect the existing button group by manipulating the `CompositeField`
already present in the `FieldList`.


```php
$fields->fieldByName('MajorActions')->push(FormAction::create('grouped', 'New group button'));
```

Another option is adding actions into the drop-up - best place for placing
infrequently used minor actions.


```php
$fields->addFieldToTab('ActionMenus.MoreOptions', FormAction::create('minor', 'Minor action'));
```

We can also easily create new drop-up menus by defining new tabs within the
`TabSet`.


```php
$fields->addFieldToTab('ActionMenus.MyDropUp', FormAction::create('minor', 'Minor action in a new drop-up'));
```

[hint]
Empty tabs will be automatically removed from the `FieldList` to prevent clutter.
[/hint]

To make the actions more user-friendly you can also use alternating buttons as
detailed in the [CMS Alternating Button](cms_alternating_button)
how-to.

## React-rendered UI
For sections of the admin that are rendered with React, Redux, and GraphQL, please refer
to [the introduction on those concepts](../reactjs_redux_and_graphql/),
as well as their respective How-To's in this section.

### Implementing handlers

Your newly created buttons need handlers to bind to before they will do anything.
To implement these handlers, you will need to create a `LeftAndMainExtension` and add
applicable controller actions to it:


```php
use SilverStripe\Admin\LeftAndMainExtension;

class CustomActionsExtension extends LeftAndMainExtension
{

    private static $allowed_actions = [
        'sampleAction'
    ];

    public function sampleAction()
    {
        // Create the web
    }

}

```

The extension then needs to be registered:


```yaml
SilverStripe\Admin\LeftAndMain:
  extensions:
    - CustomActionsExtension
```

You can now use these handlers with your buttons:


```php
$fields->push(FormAction::create('sampleAction', 'Perform Sample Action'));
```

## Summary

In a few lines of code, we've customised the look and feel of the CMS.

While this example is only scratching the surface, it includes most building
blocks and concepts for more complex extensions as well.

## Related

 * [Reference: CMS Architecture](../cms_architecture)
 * [Reference: Layout](../cms_layout)
 * [Rich Text Editing](/developer_guides/forms/field_types/htmleditorfield)
 * [CMS Alternating Button](cms_alternating_button)
