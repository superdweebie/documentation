Basic Use
=========

Validators are used to determine if a value is valid/invalid, and also return a set
of validation messages.

Each validator implements from `ValidatorInterface`, which defines an `isValid` method. To validate a
value, simply pass it to `isValid`. Eg::

    $resultObject = $validator->isValid($value);

The validator will return a `ValidatorResult` object which has two methods::

    $result = $resultObject->getResult();
    $messages = $resultObject->getMessages();

The `result` will always be a boolean indicating validitity. `messages` will be an
array of strings.

All the validators also inherit from :doc:`abstractValidator` which adds further function to validators.

:doc:`validatorGroup` can be used to chain multiple validators together into a single compound validator.

Validator Factory
^^^^^^^^^^^^^^^^^

The `ValidatorFactory` can be used to create validators from a config array. It has one static method, ``create``, that takes
two arguments. The arguments can be used in several different ways.

Return an instance of ``AlphaValidator``::

    $validator = ValidatorFactory::create('Sds\Validator\AlphaValidator');

Return an instance of ``LengthValidator`` with ``max`` and ``min`` options set::

    $validator = ValidatorFactory::create('Sds\Validator\LengthValidator', ['min' => 5, 'max' => 10]);

Return an instance of ``LengthValidator`` with ``max`` and ``min`` options set using alternate syntax::

    $validator = ValidatorFactory::create([
        'class' => Sds\Validator\LengthValidator',
        'options' => ['min' => 5, 'max' => 10]
    ]);

Return an instance of ``ValidatorGroup`` containing an ``AlphaValidator`` and ``LengthValidator``::

    $validator = ValidatorFactory::create([
        'Sds\Validator\AlphaValidator',
        [
            'class' => Sds\Validator\LengthValidator',
            'options' => ['min' => 5, 'max' => 10]
        ]
    ]);