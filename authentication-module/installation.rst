Installation
============

The easiest way to install is using composer. Add this to your `composer.json`::

    "require": {
        "superdweebie/authentication-module" : "dev-master"
    }

You will need this in your `composer.json` also::

    "minimum-stability": "dev"

Then edit your zf2 `appication.config.php` to add `Sds\AuthenticationModule`.::

    'modules' => [
        'Sds\AuthenticationModule',         
    ],

A thin zend framework 2 module that wraps an AuthenticationService for on or both of:

* AJAX based authentication
* HTTP basic authentication

By default is set up to work with the DoctrineMongoORMModule authentication adapter.

For a dojo based client that consumes this authenticaion service, see superdweebie/sijit.

Module is also configured to use Sds accessControl if desired.

#Installation

    -Add the module to your composer.json:

    "require": {
		"superdweebie/authentication-module": "dev-master",
    }

    -Edit application.config.php and add an entry for `Sds\authenticationModule`

#Use

Override values in `module.config.php` to match your system.

To get the active identity in a view or controller, just use

    $identity = $this->identity();

Retrieve the JsonRpc SMD with a GET request to `/authentication`. Use the returned SMD to make login and
logout requests to the server.