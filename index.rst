.. Sds documentation master file, created by
   sphinx-quickstart on Wed Dec 19 09:01:46 2012.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to Sds's documentation!
*******************************

.. toctree::
   :hidden:

   access-control-module/index
   authentication-module/index
   common/index
   doctrine-extensions/index
   doctrine-extensions-module/index
   dojo/index
   identity-module/index
   sijit/index
   validator/index

Sds is a collection of libraries that aim to extend and glue together a range
of different technologies orientated towards rich internet applications. Although
the pieces are designed to be used together, they are loosely coupled. Many may
find some useful components, without using the complete collection.

The technologies glued together are:

* `twitter bootstrap <http://twitter.github.com/bootstrap/>`_ client side look and feel
* `dojo <http://dojotoolkit.org/>`_ client side javascript framework
* `zendframework2 <http://framework.zend.com/>`_ server side php framework
* `doctrineMongoODM <http://docs.doctrine-project.org/projects/doctrine-mongodb-odm/en/latest/index.html>`_ php object to document mapper for mongoDB

Sds does not do every aspect of integration between these libraries. Where possible, Sds leverages existing
integrations, such as `dojo-bootstrap <https://github.com/xsokev/Dojo-Bootstrap/>`_ and `DoctrineMongoODMModule <https://github.com/doctrine/DoctrineMongoODMModule>`_.

All Sds libraries support `composer <http://getcomposer.org/>`_ for **installation**, and that is the recommended
instalation method. For specific instructions, see the docs of individual libraries.

All **source** can be found at http://github.com/superdweeibe, including the source for this documentation.

To **report a bug** or request something, please raise an issue on github against the relevant repo.

**Contributions** are very welcome, from typo corrections in the docs
to major patches. Just fork the relevant repo on github, and then pull
request back whenever you are ready.

Sds Libraries
-------------

:doc:`Access Control Module <access-control-module/index>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

:doc:`Authentication Module <authenticationccess-control-module/index>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

:doc:`Common <common/index>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Library of common code used by other Sds libaries.

:doc:`Doctrine Extensions <doctrine-extensions/index>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A collection of php 5.4 traits, Documents, Annotations, Subscribers and Filters
that can be used to extend DoctrineMongoODM.

:doc:`Doctrine Extensions Module <doctrine-extensions-module/index>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A Zend Framework 2 module for doctrineExtensions.

:doc:`Sds Dojo <dojo/index>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sds Dojo is a very small helper library for installing the Dojo javascript
framework and several other third party dojo packages via `composer <http://getcomposer.org/>`_.

:doc:`Sds Identity Module<identity-module/index>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

:doc:`sijit (dojo/Sds)<sijit/index>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

:doc:`Validator <validator/index>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A collection of validators.

Indices and tables
------------------

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

