Installation
============

The easiest way to install is using composer. Add this to your `composer.json`::

    "require": {
        "superdweebie/identity-module" : "dev-master"
    }

You will need this in your `composer.json` also::

    "minimum-stability": "dev"

Then edit your zf2 `appication.config.php` to add `Sds\IdentityModule`.::

    'modules' => [
        'Sds\IdentityModule',         
    ],
