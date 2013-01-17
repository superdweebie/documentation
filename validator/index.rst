Validator
=========

.. toctree::
   :hidden:

   installation
   basicUse
   doctrineExtensionsIntegration
   sijitIntegration
   abstractValidator
   alphaValidator
   validatorGroup

A collection of validators.

Using Validator
---------------

* :doc:`installation`
* :doc:`basicUse`
* :doc:`doctrineExtensionsIntegration`
* :doc:`sijitIntegration`

Validator Reference
-------------------

:doc:`Abstract Validator <abstractValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The class that all other validators must inherit from.

:doc:`Alpha Validator<alphaValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Validates for only alph characters.

:doc:`Credential Validator<credentialValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Validates a credential, normall a password.

:doc:`Currency Validator<currencyValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Validates for currency format.

:doc:`Email Address Validator<emailAddressValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Validates for a well formed email address.

:doc:`Identifier Array Validator<identifierArrayValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Validates for an array of Identifers.

:doc:`Identifier Validator<identifierValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Validates for an Identifer.

:doc:`Inequality Validator<inequalityValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Validates an inequality such as <, >, =<, =>, or !.

:doc:`Length Validator<lengthValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Validates string length.

:doc:`Not Required Validator<notRequiredValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Allows null values to be valid.

:doc:`Personal Name Validator<personalNameValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Validates for the name of a person.

:doc:`Required Validator<requiredValidator>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Validates null values to be in-valid.

:doc:`Validator Group<validatorGroup>`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Allows multiple validators to be chained together into a single compound validator.