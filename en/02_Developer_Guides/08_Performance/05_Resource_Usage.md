---
title: Resource Usage
summary: Manage Silverstripe CMS's memory footprint and CPU usage.
icon: tachometer-alt
---

# Resource Usage

Silverstripe CMS tries to keep its resource usage within the documented limits 
(see the [server requirements](../../getting_started/server_requirements)).

These limits are defined through `memory_limit` and `max_execution_time` in the PHP configuration. They can be 
overwritten through `ini_set()`.

[alert]
Most shared hosting providers will have maximum values that can't be altered.
[/alert]

For certain tasks like synchronizing a large `assets/` folder with all file and folder entries in the database, more 
resources are required temporarily. In general, we recommend running resource intensive tasks through the 
[command line](../cli), where configuration defaults for these settings are higher or even unlimited.

[info]
Silverstripe CMS can request more resources through `Environment::increaseMemoryLimitTo()` and
`Environment::increaseTimeLimitTo()` functions.
[/info]

```php
use SilverStripe\Core\Environment;

public function myBigFunction() 
{
    Environment::increaseTimeLimitTo(400);
}
```
