Стандарти автозавантаження
==========================

> **Застаріло** - 2014-10-21 PSR-0 відмічено як застарілий. Наразі рекомендується використовувати [PSR-4].

[PSR-4]: http://www.php-fig.org/psr/psr-4/

Нижче вкзані обов'язкові вимоги, які мають бути дотримані для
сумісності автозавантажувача.

Обов'язково
-----------

* Повне ім'я простору з класом ПОВИННІ мати наступну структуру
  `\<Vendor Name>\(<Namespace>\)*<Class Name>`
* Кожне ім'я простору ПОВИННЕ містити ім'я верхнього рівня ("Назва постачальника")
* Кожне ім'я простору може мати будь-яку кількість підпросторів.
* Кожний роздільник імен простору перетворюєтся в `DIRECTORY_SEPARATOR`
  при підключенні файлу.
* Кожний символ `_` у НАЗВІ КЛАСУ перетворюється в `DIRECTORY_SEPARATOR`.
  Символ `_` не має спеціального значення у імені простору.
* Повне ім'я простору та клас доповнються суфіксом `.php` при завантаженні
  з файлової системи.
* Літерні символи в іменях постачальників, іменах просторів та назвах класів
  можуть бути будь-якою комбінацією літер у нижньому та верхньому регістрі.

Приклади
--------

* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`


Символ підкреслення у іменах простору та назвах класів
------------------------------------------------------

* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`

Стандарти, які ми встановлюємо, мають бути найменшим спільним знаменником для
безболісної сумісності автозавантажувача. Ви можете перевірити, що ви
відповідаєте цим стандартам, використовуючи реалізазацію класа-приклада
SplClassLoader, який може завантажувати класи PHP 5.3.

Приклад реалізації
------------------

Нижче наведена функція для демонстрації як вищевказані стандарти
використовуются для автозавантаження класів.

```php
<?php

function autoload($className)
{
    $className = ltrim($className, '\\');
    $fileName  = '';
    $namespace = '';
    if ($lastNsPos = strrpos($className, '\\')) {
        $namespace = substr($className, 0, $lastNsPos);
        $className = substr($className, $lastNsPos + 1);
        $fileName  = str_replace('\\', DIRECTORY_SEPARATOR, $namespace) . DIRECTORY_SEPARATOR;
    }
    $fileName .= str_replace('_', DIRECTORY_SEPARATOR, $className) . '.php';

    require $fileName;
}
spl_autoload_register('autoload');
```

Реалізація SplClassLoader
-------------------------

Наступний gist є прикладом реалізації SplClassLoader, який може
завантажувати ваші класи, якщо ви слідуєте вищевказаним стандартам.
Наразі це рекомендований спосіб завантажувати класи PHP 5.3, які
слідують цим стандартам.

* [http://gist.github.com/221634](http://gist.github.com/221634)
