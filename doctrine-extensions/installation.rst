Installation
============

The easiest way to install is using composer. Add this to your `composer.json`::

    "require": {
        "superdweebie/doctrine-extensions" : "dev-master"
    }

Note that you do *not* need `doctrine/mongodb-odm` in your `composer.json`.

You will need this in your `composer.json` also::

    "minimum-stability": "dev"

Doctrine Extensions will install all doctrine dependencies. It also assumes that
you have an instance of `mongoDB<http://mongodb.org>`_ running, and the `mongoDB php driver<http://www.mongodb.org/display/DOCS/PHP+Language+Center>`_ installed.
