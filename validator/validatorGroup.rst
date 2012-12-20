Validator Group
===============

Allows multiple validators to be chained together into a single compound validator.

To create a ``ValidatorGroup``, pass the constructor an array of validators. Eg::

    $validator1 = new AlphaValidator;
    $validator2 = new LengthValidator(5, 10);

    $validatorGroup = new ValidatorGroup([$validator1, $validator2]);

    $resultObject = $validatorGroup->isValid($value);

``ValidatorGroup`` will evaluate each validator in turn. If all validators return `true`, then
``ValidatorGroup`` will return true. If a single validator returns `false` then ``ValidatorGroup`` will
return false. The messages that ``ValidatorGroup`` returns is a merged array of all the messages from all the validators.

See also
^^^^^^^^

* :doc:`\sijit\validator\validatorGroup`