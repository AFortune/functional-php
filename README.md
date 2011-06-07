# Functional PHP: Functional primitives for PHP

  - Works with arrays and everything implementing interface Traversable
  - Consistent interface: first parameter is always the collection, than the callback. Callbacks always get value, key,
    collection passed
  - Calls 5.3 closures as well as traditional callbacks
  - C implementation for performance but compatible userland implementation is packaged if you can’t install PHP
    extensions
  - All functions reside in namespace `Functional` to not conflict with any other extension or library

## TODO
 - Native implementation of `Functional\invoke()` shows slightly different behavior
 - Implement `Functional\reduce_right()` natively

## Installation

### Install native extension
```bash
cd functional-php/extension/
phphize
./configure
make
sudo make install
```

### Use userland extension
```php
<?php
include 'path/to/functional-php/src/Functional/_import.php';
```

Everytime you want to use Functional PHP and not reference the fully qualified name, add `use Functional;` on top of
your PHP file.


## Overview
### Functional\all() & Functional\invoke()

``Functional\all(array|Traversable $collection, callable $callback)``

``Functional\invoke(array|Traversable $collection, string $methodName[, array $methodArguments])``

```php
<?php
use Functional;

// If all users are active, set them all inactive
if (all($users, function($user, $collectionKey, $collection) {return $user->isActive();})) {
    invoke($users, 'setActive', array(true));
}
```


### Functional\any()

``Functional\any(array|Traversable $collection, callable $callback)``

```php
<?php
use Functional;

if (any($users, function($user, $collectionKey, $collection) use($me) {return $user->isFriendOf($me);})) {
    // One of those users is a friend of me
}
```


### Functional\none()

``Functional\none(array|Traversable $collection, callable $callback)``

```php
<?php
use Functional;

if (none($users, function($user, $collectionKey, $collection) {return $user->isActive();})) {
    // Do something with a whole list of inactive users
}
```


### Functional\reject() & Functional\select()

``Functional\select(array|Traversable $collection, callable $callback)``

``Functional\reject(array|Traversable $collection, callable $callback)``

```php
<?php
use Functional;

$fn = function($user, $collectionKey, $collection) {
    return $user->isActive();
};
$activeUsers = select($users, $fn);
$inactiveUsers = reject($users, $fn);
```


### Functional\pluck()
Fetch a single property from a collection of objects.

``Functional\pluck(array|Traversable $collection, string $propertyName)``

```php
<?php
use Functional;

$names = pluck($users, 'name');
```

### Functional\reduce_left() & Functional\reduce_right()
Applies a callback to each element in the collection and reduces the collection to a single scalar value.
`Functional\reduce_left()` starts with the first element in the collection, while `Functional\reduce_right()` starts
with the last element.

``Functional\reduce_left(array|Traversable $collection, callable $callback[, $initial = null])``
``Functional\reduce_right(array|Traversable $collection, callable $callback[, $initial = null])``

```php
<?php
use Functional;

// $sum will be 64 (2^2^3)
$sum = reduce_left(array(2, 3), function($value, $key, $collection, $reduction) {
    return $reduction ^ $value;
}, 2);

// $sum will be 512 (2^3^2)
$sum = reduce_right(array(2, 3), function($value, $key, $collection, $reduction) {
    return $reduction ^ $value;
}, 2);
```

### Additional functions:

 - `Functional\each(array|Traversable $collection, callable $callback)`
   Applies a callback to each element
 - `Functional\map(array|Traversable $collection, callable $callback)`
   Applies a callback to each element in the collection and collects the return value

