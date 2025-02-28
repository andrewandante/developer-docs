---
title: Release process
summary: Describes the process followed for "core" releases.
iconBrand: git-alt
---

# Release Process

This page describes the process followed for "core" releases
(the modules included by [silverstripe/recipe-core](https://github.com/silverstripe/recipe-core)
and [silverstripe/recipe-cms](https://github.com/silverstripe/recipe-cms)).

## Release Planning

The Silverstripe CMS core team uses an Agile methodology. We aim to keep the latest development branches of each 
module in a "potentially releasable" state. Releases are planned around an issue backlog according to our 
[roadmap](https://www.silverstripe.org/software/roadmap/).  We re-revaluate our priorities every few weeks.

Silverstripe CMS is split up into many modules listed on [github.com/silverstripe](https://github.com/silverstripe).
Each may have a different release line (e.g. 2.x vs. 5.x).
There are high-level "recipe" milestones on the [framework repository](https://github.com/silverstripe/silverstripe-framework/milestones)
to combine individual module milestones into a larger release that's eventually available to install with 
[composer](https://silverstripe.org/download).

New features and API changes are discussed as
[GitHub issues](https://docs.silverstripe.org/en/contributing/issues_and_bugs/)),
as well as the [forum](https://forum.silverstripe.org). 
They are prioritised by the core team as tickets on 
github.com. In addition, we collect community feedback as [feature ideas](https://forum.silverstripe.org/c/feature-ideas) on the forum.
Any feature ideas we're planning to implement will be flagged there.

## Keeping Track

Release dates are usually not published prior to the release, but you can get a good idea of the release status by
reviewing the release milestone on github.com. Releases will be
announced on the ["releases" forum category](https://forum.silverstripe.org/c/releases).
Significant releases will also be published via the [silverstripe.org blog](https://silverstripe.org/blog).

You can also keep track of new releases through the CMS UI
by installing the [maintenance module](https://github.com/bringyourownideas/silverstripe-maintenance),
or using the [composer outdated](https://getcomposer.org/doc/03-cli.md#outdated) command in your project.

Additionally, you can [keep track of issues](issues_and_bugs#identifying-issues) (both open and closed)
across all modules involved in a recipe release.

## Release numbering {#release-numbering}

Silverstripe CMS follows [Semantic Versioning](https://semver.org).

All Silverstripe CMS modules (including `silverstripe/framework`) may have patch releases created at any time, and will
not necessarily match other core module patch release numbers. For example, your project may be using
`silverstripe/cms` 5.3.1 with `silverstripe/versioned` 5.3.9.

All Silverstripe CMS recipes are released in lock step with each other. For example, `silverstripe/installer` 5.3.1 will
contain `silverstripe/recipe-cms` 5.3.1 and `silverstripe/recipe-core` 5.3.1. These recipes may contain various patch
versions of its modules within the same minor release line (5.3 in this example).

## Regular quarterly releases

A regular Silverstripe CMS release is tagged on a quarterly basis, in the months: March, June, September, December. This usually involves creating a new minor increment of 
each core recipe. Regular quarterly releases are preceded by pre-stable releases to help lay the groundwork for the
stable release.

### Beta phase

At the start of the Beta phase:
* new minor branches for core modules are forked from the major branches
* a feature freeze is instituted on the minor branches
* a `beta1` release is tagged for recipes and related modules.

The length of the beta phase period will be communicated at the time it is tagged via the [Silverstripe Forum](https://forum.silverstripe.org/c/releases). While the beta phase lasts additional betas may be released to address critical issues holding back
testers.

#### "Beta release" definition

The purpose of a beta release is to initiate a "Feature Freeze" to stabilise the Silverstripe CMS codebase for an upcoming stable release and showcase 
new features. A beta release is likely to contain regressions and new bugs. A beta release is primarily aimed at testers 
who want to help identify bugs and regressions.

#### "Feature freeze" definition

A "Feature freeze" is a pre-release minor branch state. This state allows the core team to stabilise the upcoming 
release and reach production quality.

The following type of changes can be merged in a branch in feature freeze:
* bug fixes, regardless of severity
* API changes if they fix a bug and are compliant with our existing semver commitments
* merge-ups from older minor branches (introducing bug fixes).

The following changes cannot be merged in a branch in feature freeze:
* anything that introduces new features
* API changes not targeted to fixing a bug, or that alter an existing API in a stable release.

### Release Candidate phase

All critical or high priority issues in the beta release should have been identified and fixed by the start of the
Release Candidate phase.

At the start of the Release Candidate phase:
* a publicly visible `rc1` release is tagged for recipes and related modules
* a private Release Candidate is created containing fixes for undisclosed vulnerabilities on top of the `rc1` release
* the private Release Candidate is delivered to independent auditors for a security review.

The Release Candidate phase lasts approximately two weeks. Additional patches may be added to the final release only if:
* vulnerabilities are discovered by the security audit
* additional critical issues are identified. 

#### "Release Candidate" definition

A release candidate will not introduce any new features compared to its preceding beta release. The Silverstripe CMS
core team is confident a release candidate is production quality and does not introduce new regressions.

The purpose of a release candidate is to allow module maintainers and project owners to audit the upcoming release and
confidently plan their upgrades.

Only critical bug fixes can be added to the upcoming stable release once the release candidate is released.

### Stable phase

A new stable minor release is tagged for Silverstripe CMS core recipes. This marks the start of our official semver
commitment for any new APIs introduced in that minor release.

## Supported versions {#supported-versions}

At any point in time, the core development team will support a set of releases to varying levels:

* The status of major releases is determined by the [roadmap](https://silverstripe.org/roadmap)
* Minor releases of major releases in "active development" or in the "bug and security fixes" phase are released roughly every three months, and their End-of-Life (EOL) is announced at least six months in advance
* The latest minor release is supported as long as the underlying major release
* API changes and major new features are applied to the next major release branch, to be included in the next major release
* New APIs can be applied to the current minor release of major releases in "active development", but should usually be marked as "internal" APIs until they're considered stable
* Enhancements are applied to the next minor release of major releases in "active development"
* Non-critical bugfixes and all security fixes are applied to all supported minor releases of major releases in "active development" or in the "bug and security fixes" phase
* Critical bugfixes and [critical security fixes](#severity-rating) are applied to the all minor releases of major releases in "active development", "bug and security fixes" or "security fixes only"
* [Non-critical security fixes](#severity-rating) are backported to releases in the "security fixes only" phase on a best effort basis
* Any patches applied to older minor releases are merged up regularly to newer minor releases (in the same major release)
* Any patches applied to older major releases are merged up regularly to newer major releases

Note that this only applies to the "core" recipe (the modules included by [silverstripe/recipe-core](https://github.com/silverstripe/recipe-core)
and [silverstripe/recipe-cms](https://github.com/silverstripe/recipe-cms)).
For [supported modules](/project_governance/supported_modules/) outside of this recipe,
please refer to our [supported modules definition](https://www.silverstripe.org/software/addons/supported-modules-definition/).


## Deprecation

Needs of developers (both on core framework and custom projects) can outgrow the capabilities
of a certain API. Existing APIs might turn out to be hard to understand, maintain, test or stabilise.
In these cases, it is best practice to "refactor" these APIs into something more useful.
The Silverstripe CMS team acknowledges that developers have built a lot of code on top of existing APIs,
so we strive for giving ample warning on any upcoming changes through a "deprecation cycle".

### Deprecating API:

* Add a `@deprecated` item to the docblock tag, with a `{@link <class>}` item pointing to the new API to use.
* Update the deprecated code to throw a [Deprecation::notice()](api:SilverStripe\Dev\Deprecation::notice()) error.
* Deprecations should not be committed to patch releases
* Both the docblock and error message should contain the **version** where the functionality is deprecated from.
  So, if you're committing the change to a *4.12* minor release, the target version will be *4.12.0*. 
* Deprecations should only be committed to pre-release branches, ideally before they enter the "beta" phase.
  If deprecations are introduced after this point, their target version needs to be increased by one.
* Make sure that the old deprecated function works by calling the new function - don't have duplicated code!
* The commit message should contain an `API` prefix (see ["commit message format"](code#commit-messages))
* Document the change in the [changelog](/changelogs) for the next release
* Deprecated APIs can be removed only after developers have had sufficient time to react to the changes. Hence, deprecated APIs should be removed in MAJOR releases only. Between MAJOR releases, leave the code in place with a deprecation warning. 
* Exceptions to the deprecation cycle are APIs that have been moved into their own module, and continue to work with the new minor release. These changes can be performed in a single minor release without a deprecation period.

When deprecating a method:

* Add the following docblock `@deprecated 1.2.3 Use anotherMethod() instead`
* Deprecation::notice('1.2.3', 'Use anotherMethod() instead'); to the top of the method

When deprecating a class:

* Add the following docblock `@deprecated 1.2.3 Use AnotherClass instead`
* Add `Deprecation::notice('1.2.3', 'Use AnotherClass instead', Deprecation::SCOPE_CLASS);` to the top of `__construct()`
* Wrap `Deprecation::notice()` with `Deprecation::withNoReplacement()` if there's no replacement for that deprecated class. e.g:

```php
/**
 * @deprecated 4.12.0 Will be removed without equivalent functionality
 */
class MyDeprecatedClass extends AnotherClass
{
    public function __construct()
    {
        Deprecation::withNoReplacement(function () {
            Deprecation::notice(
                '4.12.0',
                'Will be removed without equivalent functionality',
                Deprecation::SCOPE_CLASS
            );
        });
        parent::__construct();
    }
}
```

When deprecating config:

* Add the following docblock `@deprecated 1.2.3 Use different_config instead`

When deprecating some behaviour or combination of configuration values:

* Add `Deprecation::notice('1.2.3', 'Using x with y is deprecated. Do [other thing] instead', Deprecation::SCOPE_GLOBAL);`
* It may not be immediately clear where this type of deprecation notice should go. In that case, add it to the `HTTPApplication::warnAboutDeprecatedSetups()` method.
* It may be appropriate to link to some documentation in the message for this type of deprecation notice.

If there is no immediate replacement for deprecated code that is being called, either because the replacement is not available until the next major version, or because there is not a plan for there to be a replacement, the message should be "Will be removed without equivalent functionality to replace it."

### Avoiding deprecated API

Wherever possible, once some API has been deprecated, we should stop using it. This allows projects to emit deprecation notices and have an accurate list of code that they need to change.

In some cases, we may not yet have a replacement for code that is required for the current major - but that we still have to call and support internally. In these cases, since we can't stop calling the code, wrap the call to the deprecated code in `Deprecation::withNoReplacement()` e.g:

```php
Deprecation::withNoReplacement(function () {
  $this->myDeprecatedMethod();
});
```

For any unit tests using the deprecated method/class/config, add the following the the top of the unit test. This ensures that deprecated code is still supported as usual when `Deprecation` notices are not enabled, though the tests are skipped whenever you are testing to see if deprecated code is still be called.
```php
if (Deprecation::isEnabled()) {
    $this->markTestSkipped('Test calls deprecated code');
}
```

Here's an example for replacing `Director::isDev()` with a (theoretical) `Env::is_dev()`:

```php
/**
 * Returns true if your are in development mode
 * @deprecated 4.12.0 Use Env::is_dev() instead.
 */
public function isDev() 
{
    Deprecation::notice('4.12.0', 'Use Env::is_dev() instead');
    return Env::is_dev();
}
```

This change could be committed to a minor release like *4.12.0*, and remains deprecated in all subsequent minor releases
(e.g. *4.13.0*), until a new major release (e.g. *5.0.0*), at which point it gets removed from the codebase. 

### Enabling deprecation notices

Deprecation notices aren't enabled by default.  They can be turned on for dev environments with one of the following methods:

`.env`
```
SS_DEPRECATION_ENABLED=true
```

`app/_config.php`
```php
Deprecation::enable();
```

To test that deprecated code is no longer being called, run code via CI in an installer/kitchen-sink project that has deprecations enabled. Then view the CI output in the "Run tests" of the github actions CI job and/or view the contents of silverstripe.log in the github actions CI artifact to see if there are deprecation warnings. There should be zero deprecation warnings.

## Security Releases

A security release is a [Silverstripe CMS Core Release](making_a_silverstripe_core_release#standard-release-process) with patches for one or more security issues.

### Reporting an issue

Report security issues in our [commercially supported modules](/project_governance/supported_modules/)
to [security@silverstripe.org](mailto:security@silverstripe.org). 
Please don't file security issues in our [bugtracker](issues_and_bugs). 

Silverstripe CMS does not operate a *bug bounty* program.

### Acknowledgment and disclosure

In the event of a confirmed vulnerability in our
[supported modules](/project_governance/supported_modules/),
we will take the following actions:

* Acknowledge to the reporter that we've received the report and that a fix is forthcoming.
  We'll give a rough timeline and ask the reporter to keep the issue confidential until we announce it.
* Assign a [CVE identifier](https://cve.mitre.org) to the issue.
* For "high" and "critical" issues (CVSS of >=7.0): Pre-announce the upcoming security release to a private pre-announcement mailing list of important stakeholders (see below).
* We will inform you about resolution and [announce](https://forum.silverstripe.org/c/releases) a 
[new release](https://www.silverstripe.org/security-releases/) publicly.

You can help us determine the problem and speed up responses by providing us with more information on how to reproduce
the issue: 
* Silverstripe CMS version (incl. any installed modules)
* PHP/webserver version and configuration
* anonymised webserver access logs (if a hack is suspected)
* any other services and web packages running on the same server.

### Severity rating

Each [security release](https://www.silverstripe.org/security-releases/) includes an overall severity rating and one for 
each vulnerability. The rating indicates how important an update is.
It follows the [Common Vulnerability Scoring System (CVSS)](https://www.first.org/cvss). 
This rating determines which release lines are targeted with security fixes.

| Severity      | CVSS | Description |
|---------------|------|-------------|
| **Critical**  | 9.0 to 10.0 | Critical releases require immediate action. Such vulnerabilities allow attackers to take control of your site and you should upgrade on the day of release. *Example: Directory traversal, privilege escalation* |
| **High**      | 7.0 to 8.9 | Important releases should be evaluated immediately. These issues allow an attacker to compromise a site's data and should be fixed within days. *Example: SQL injection.* |
| **Medium**    | 4.0 to 6.9 | Releases of moderate severity should be applied as soon as possible. They allow the unauthorized editing or creation of content. *Examples: Cross Site Scripting (XSS) in template helpers.* |
| **Low**       | 0.1 to 3.9 | Low risk releases fix information disclosure and read-only privilege escalation vulnerabilities. These updates should also be applied as soon as possible, but with an impact-dependent priority. *Example: Exposure of the core version number, Cross Site Scripting (XSS) limited to the admin interface.* |
| **None**      | 0.0 | These releases won't affect a large majority of projects, and may not require any action. They only impact projects with extremely complex or unlikely customisations. They do not impact projects which only implement common customisations or use common features. |

### Internal Security Process

See [Silverstripe CMS Core Release Process](making_a_silverstripe_core_release).

### Pre-announcement mailing list

In addition to our public disclosure process, we maintain a private mailing list where upcoming
"high" and "critical" security releases are pre-announced.
Members of this list will receive a security pre-announcement, as soon as it has been
sufficiently researched, with a timeline for the upcoming release. 
This will happen a few days before the announcement goes public alongside a new release, 
and most likely before a patch has been developed.

Since we'll distribute sensitive information on unpatched vulnerabilities in this list, 
the selection criteria for joining naturally has to be strict. 
Applicants should provide references within the community, 
as well as a demonstrated need for this level of information 
(e.g. involvement with a large website with sensitive customer data). 
You don’t need to be a client of Silverstripe Ltd to get on board, 
but we will need to perform some low-touch background checks to verify your identity. 
Please contact [security@silverstripe.org](mailto:security@silverstripe.org) for details.

Only "high" and "critical" issues are pre-announced via the mailing list. If you want
to know about all the minor security mitigations, keep an eye on the ["releases" forum category](https://forum.silverstripe.org/c/releases),
changelogs and the website section [Security Releases](https://www.silverstripe.org/download/security-releases/).


## Quality Assurance and Testing

The quality of our software is important to us, and we continuously test it for regressions
through a broad suite of unit and integration tests. These run on
[GitHub Actions](https://docs.github.com/en/actions), and results are publicly available.
Check the badges on the various modules available on [github.com/silverstripe](https://github.com/silverstripe).
