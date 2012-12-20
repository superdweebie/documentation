Abstract Validator
==================

All the Sds validators also inherit from ``AbstractValidator``. ``AbstractValidator`` adds four properties
boolean which are used if the validator is part of a :doc:`validatorGroup`. The four properties are:

* **haltOnPass**  If this validator is part of a :doc:`validatorGroup`, setting to true will stop any Validators after this one from executing if this validator passes.
* **haltOnFail** If this validator is part of a :doc:`validatorGroup`, setting to true will stop any Validators after this one from executing if this validator fails.
* **skipOnPass** If this validator is part of a :doc:`validatorGroup`, setting to true will skip evaluating this validator, if all previous validators have already evaluated to true.
* **skipOnFail** If this validator is part of a :doc:`validatorGroup`, setting to true will skip evaluating this validator, if a previous validator has already evaluated to false.

See also
^^^^^^^^

* :doc:`\sijit\validator\baseValidator`
