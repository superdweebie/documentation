Installation
============

The easiest way to install is using composer. Add this to your `composer.json`::

    "require": {
        "superdweebie/zf2-extensions-module" : "dev-master"
    }

You will need this in your `composer.json` also::

    "minimum-stability": "dev"

Then edit your zf2 `appication.config.php` to add `Sds\Zf2ExtensionsModule`. When using other
Sds zf2 modules, `Sds\Zf2ExtensionsModule` should be registered first.::

    'modules' => [
        'Sds\Zf2ExtensionsModule',         
    ],
