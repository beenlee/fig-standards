## 译文：

自动加载标准
====================

> **已过时** - PSR-0 已经在2014-10-21日被弃用。 推荐使用[PSR-4]。

[PSR-4]: http://www.php-fig.org/psr/psr-4/
  
下面介绍了使autoloader具有互操作性所必须遵守的条件。


必要条件 
---------

* 一个完整有效的 `命名空间` 和 `类` 必须遵循以下结构： `\<Vendor Name>\(<Namespace>\)*<Class Name>`
* 每个 `命名空间` 必须包含一个顶级的 `命名空间（"Vendor Name"）`
* 每个 `命名空间` 可以拥有任意多个 `子命名空间`
* 当从文件系统中加载文档时，每一个命名空间中的 `分隔符（separator）` 都要被转换为`DIRECTORY_SEPARATOR`
* 所有的 `CLASS NAME(类名)` 中的字符 `_`，都要被转换为`DIRECTORY_SEPARATOR`。且字符 `_` 在命名空间中并没有特殊含义。
* 当从文件系统中加载文档时，完整有效的命名空间和类要以 `.php` 结尾。
* vendor names, namespaces, 和 class names 都可以由大写或者小写的英文字母组合而成。

例子
--------

* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`

命名空间与类名中需要强调的
-----------------------------------------

* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`

我们在标准中所规定的，应当是对于无副作用的互操作性autoloader来说最基本特性。你可以试着遵循这些标准，实现样例 SplClassLoader去加载php5.3的类。


样例实现
----------------------

以下是一个示例函数来简单的展示以上提到的标准是如何进行自动加载的。

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

SplClassLoader 方式实现 
-----------------------------

下边是一个以SplClassLoader形式实现的，并且遵循前边提到的 autoloader 互操作性标准的加载类的例子，这也正是当前被推荐的遵循标准的加载PHP 5.3 类的方式。
* [http://gist.github.com/221634](http://gist.github.com/221634)

---

## 原文：


Autoloading Standard
====================

> **Deprecated** - As of 2014-10-21 PSR-0 has been marked as deprecated. [PSR-4] is now recommended 
as an alternative.    

[PSR-4]: http://www.php-fig.org/psr/psr-4/

The following describes the mandatory requirements that must be adhered
to for autoloader interoperability.    


Mandatory
---------

* A fully-qualified namespace and class must have the following
  structure `\<Vendor Name>\(<Namespace>\)*<Class Name>`    
* Each namespace must have a top-level namespace ("Vendor Name").    
* Each namespace can have as many sub-namespaces as it wishes.    

* Each namespace separator is converted to a `DIRECTORY_SEPARATOR` when
  loading from the file system.    
  
* Each `_` character in the CLASS NAME is converted to a
  `DIRECTORY_SEPARATOR`. The `_` character has no special meaning in the
  namespace.    
  
* The fully-qualified namespace and class is suffixed with `.php` when
  loading from the file system.    
 
* Alphabetic characters in vendor names, namespaces, and class names may
  be of any combination of lower case and upper case.    
  
Examples
--------

* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`

Underscores in Namespaces and Class Names
-----------------------------------------

* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`

The standards we set here should be the lowest common denominator for
painless autoloader interoperability. You can test that you are
following these standards by utilizing this sample SplClassLoader
implementation which is able to load PHP 5.3 classes.    


Example Implementation
----------------------

Below is an example function to simply demonstrate how the above
proposed standards are autoloaded.    


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

SplClassLoader Implementation
-----------------------------

The following gist is a sample SplClassLoader implementation that can
load your classes if you follow the autoloader interoperability
standards proposed above. It is the current recommended way to load PHP
5.3 classes that follow these standards.    

* [http://gist.github.com/221634](http://gist.github.com/221634)
