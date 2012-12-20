Doctrine Extensions Integration
===============================

The :doc:`/doctrine-extensions/validator` extension uses the Sds Validator library. It
allows validators to be defined against documents with annotations, and can then
validate documents before they are commited to the database.

The :doc:`/doctrine-extensions/dojo` extension can use the validator annotations placed
on documents to generate :doc:`/sijit/index` compatable dojo code that uses the sijit
validator library (:doc:`/sijit/validator`) so that the same validators can be executed
both server and client side.
