# PHP Doctrine ORM 
# A summary of working with objects

https://www.doctrine-project.org/projects/doctrine-orm/en/2.7/reference/working-with-objects.html


# 1. Persisting entities (`INSERT` object/entity)

```php
<?php

$user = new User;
$user->setName('Mr.Right');

// $em instanceof EntityManager

$em->persist($user);
$em->flush();

```

# 2. Removing entities (`DELETE` object/entity)

```php
<?php

// $em instanceof EntityManager

$em->remove($user);
$em->flush();

```

# 3. Querying (`SELECT` object/entity)

## `3.1 By primary key`

3.1.1 The most basic way to query for a persistent object is by its identifier / primary key using the `EntityManager#find($entityName, $id)` method:
```php
<?php

// $em instanceof EntityManager

$user = $em->find('MyProject\Domain\User', $id);

```

3.1.2 Essentially, `EntityManager#find()` is just a shortcut for the following:

```php
<?php

// $em instanceof EntityManager

$user = $em->getRepository('MyProject\Domain\User')->find($id);
```
## `3.2 By simple condition`
3.2.1 To query for one or more entities based on several conditions that form a logical conjunction, use the `findBy` and `findOneBy` methods on a repository as follows:

```php
<?php

// $em instanceof EntityManager

// All users that are 20 years old
$users = $em->getRepository('MyProject\Domain\User')->findBy(array('age' => 20));

// All users that are 20 years old and have a surname of 'Miller'
$users = $em->getRepository('MyProject\Domain\User')->findBy(array('age' => 20, 'surname' => 'Miller'));

// A single user by its nickname
$user = $em->getRepository('MyProject\Domain\User')->findOneBy(array('nickname' => 'romanb'));

```

3.2.2 You can also load by owning side associations through the repository:

```php
<?php

$number = $em->find('MyProject\Domain\Phonenumber', 1234);

$user = $em->getRepository('MyProject\Domain\User')->findOneBy(array('phone' => $number->getId()));

```

3.2.3 The `EntityRepository#findBy()` method additionally accepts orderings, limit and offset as second to fourth parameters:

```php
<?php

$tenUsers = $em->getRepository('MyProject\Domain\User')->findBy(array('age' => 20), array('name' => 'ASC'), 10, 0);

```

3.2.4 If you pass an array of values Doctrine will convert the query into a `WHERE field IN (..)` query automatically:

```php
<?php

$users = $em->getRepository('MyProject\Domain\User')->findBy(array('age' => array(20, 30, 40)));

// translates roughly to: SELECT * FROM users WHERE age IN (20, 30, 40)

```

3.2.5 An EntityRepository also provides a mechanism for more concise calls through its use of `__call`. Thus, the following two examples are equivalent:

```php
<?php

// A single user by its nickname
$user = $em->getRepository('MyProject\Domain\User')->findOneBy(array('nickname' => 'romanb'));

// A single user by its nickname (__call magic)
$user = $em->getRepository('MyProject\Domain\User')->findOneByNickname('romanb');

```

3.2.6 Additionally, you can just count the result of the provided conditions when you don't really need the data:

```php
<?php

// Check there is no user with nickname
$availableNickname = 0 === $em->getRepository('MyProject\Domain\User')->count(['nickname' => 'nonexistent']);

```
