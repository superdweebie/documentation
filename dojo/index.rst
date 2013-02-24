Sds Dojo
========

Sds Dojo is a very small helper library for installing the Dojo javascript
framework and several other third party dojo packages via composer.

Dojo packages are not available on http://packagist.com, so to use them, you will need
to add the following to your project's root `composer.json`::

   "repositories": [{ "type": "composer", "url": "https://raw.github.com/superdweebie/dojo/master" }]

Requireing Dojo
---------------

To require dojo, add the following to your `composer.json`::

    "require": {
        "dojo/dojo"   : "1.8.3",
        "dojo/dijit"  : "1.8.3",
        "dojo/dojox"  : "1.8.3",
        "dojo/util"   : "1.8.3"
    }

Alternately, you can use this for shorthand. It has the same effect as the
`require` above::

    "require": {
        "superdweebie/dojo" : "1.8.3"
    }

Running `composer.phar` will create and fill the following directories::

    vendor/dojo/dojo
    vendor/dojo/dijit
    vendor/dojo/dojox
    vendor/dojo/util

Using Dojo
----------

This composer install places dojo in your `vendor` directory, which should
not be publically accessable. So, how do you make dojo available to your
client?

In a development environment, it is recomended you expose the `vendor/dojo`
directory in one of two ways:

* VirtualHosts configuration.
* Symlink

In a production environment, it is not recomended you expose the `vendor/dojo`
directory. Instead, build dojo (which you should be doing anyway) and place
your built dojo in a publically accessable location.

Third Party Packages
--------------------

Sijit
^^^^^

:doc:`Sijit </sijit/index>` is a library of dojo extensions which is part of Sds.

Depending which version you require, install sijit, with::

    "require": {
        "dojo/Sds" : "0.1"
    }

or::

    "require": {
        "dojo/Sds" : "dev-master"
    }

This will install sijit to `vendor/dojo/Sds`

Dojo-bootstrap
^^^^^^^^^^^^^^

This is a port of twitter bootstrap into the dojo framework. See the
github repo here: http://github.com/xsokev/Dojo-Bootstrap. It is a
dependency of sijit.

Install with::

    "require": {
        "dojo/bootstrap" : "v1.0"
    }

or::

    "require": {
        "dojo/bootstrap" : "v1.2"
    }

or::

    "require": {
        "dojo/bootstrap" : "dev-master"
    }

This will install dojo-bootstrap to `vendor/dojo/bootstrap`

Twitter bootstrap
^^^^^^^^^^^^^^^^^

This is twitter bootstrap itself. See the github repo here:
http://github.com/twitter/bootstrap

Install with::

    "require": {
        "twitter/bootstrap" : "2.2.2"
    }

or::

    "require": {
        "twitter/bootstrap" : "2.1.1"
    }

or::

    "require": {
        "twitter/bootstrap" : "2.1.0"
    }

This will install twitter bootstrap to `vendor/twitter/bootstrap`

Font-awesome
^^^^^^^^^^^^

Iconic font designed for twitter bootstrap. See the github repo here:
http://github.com/FortAwesome/Font-Awesome

Install with::

    "require": {
        "fort-awesome/font-awesome" : "2.0.0"
    }

or::

    "require": {
        "fort-awesome/font-awesome" : "3.0.1"
    }

This will install Font-awesome to `vendor/fort-awesome/font-awesome`
