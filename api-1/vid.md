# Вид

View-это класс, отвечающий за рендеринг PHP-представлений \(на языке MVC\).

Пространство имен: `\`  
Расположение файла: `lib/base.php`

### Что такое вид? <a id="what-is-a-view"></a>

Представление - это представление данных вашего проекта. Считается лучшей практикой отделять код, генерирующий данные, от кода, генерирующего представление.

Например, давайте рассмотрим, как вы могли бы создать карту сайта sitemap в традиционном PHP:

```text
// first we generate the list of URLs (Web pages)
$urls=array('http://domain.tld/home','http://domain.tld/contact','http://domain.tld/about');

// at the end of the code, we display the list as an HTML view:
header('Content-Type: text/html; charset=UTF-8');
echo "<html><table><tr><td>$urls[0]</td></tr><tr><td>$urls[1]</td></tr><tr><td>$urls[2]</td></tr></table></html>";

// or as a XML view:
header('Content-Type: text/xml; charset=UTF-8');
echo "<?xml><urlset><url><loc>$urls[0]</loc></url><url><loc>$urls[1]</loc></url><url><loc>$urls[2]</loc></url></urlset>";

// or as a CSV view:
header('Content-Type: text/csv; charset=UTF-8');
echo implode("\r\n",$urls);
```

### Визуализация вида <a id="render-a-view"></a>

Класс View позволяет легко разделить два шага, определенных выше, так как он требует рендеринга имени файла и улья данных:

```text
string render ( string $file [, string $mime = 'text/html' [, array $hive = NULL, [ int $ttl = 0 ]]] )
```

**Примечание:**  
`$mime`Аргумент отвечает за создание правильного автоматически генерируемого `Content-type`заголовка.  
Если данные не `$hive`предоставляются, используется глобальный улей F3.

Вот как использовать его в вашем обработчике маршрута с помощью улья F3:

```text
$f3->set('urls',
    array(
        'http://domain.tld/home',
        'http://domain.tld/contact',
        'http://domain.tld/about'
    )
);
$view=\View::instance();
echo $view->render('myview.html','text/html');
// or
echo $view->render('myview.xml','text/xml');
// or
echo $view->render('myview.csv','text/csv');
```

Если вы предпочитаете передавать только соответствующие переменные в свое представление:

```text
$urls=array('http://domain.tld/home','http://domain.tld/contact','http://domain.tld/about');
$view=\View::instance();
echo $view->render('myview.html','text/html',array('urls'=>$urls));
// or
echo $view->render('myview.xml','text/xml',array('urls'=>$urls));
// or
echo $view->render('myview.csv','text/csv',array('urls'=>$urls));
```

`myview.html`

```text
<html>
<head>
...
</head>
<body>
<table>
<?php foreach($urls as $url):?>
  <tr>
    <td>
      <?php echo $url;?>
    </td>
  </tr>
<?php endforeach;?>
</body>
</html>
```

`myview.xml`

```text
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
<?php foreach($urls as $url):?>
  <url>
    <loc>
      <?php echo $url;?>
    </loc>
  </url>
<?php endforeach;?>
</urlset>
```

`myview.csv`

```text
<?php foreach($urls as $url):?>
<?php echo $url;?>
<?php endforeach;?>
```

### Методы <a id="methods"></a>

#### afterrender <a id="afterrender"></a>

**вызовите функцию после визуализации представления, прежде чем она будет записана в `/tmp`хранилище**

```text
string afterrender ( string|callback $func )
```

Вызовите функцию, указанную в `$func`разделе .

При вызове функции визуализированное представление передается функции в виде строки. Вызываемая функция должна возвращать измененное визуализированное представление после того, как любая модификация будет выполнена из функции обратного вызова. Вы можете вызвать этот крюк несколько раз, чтобы назначить более одной функции, если это необходимо. Использует [Base - &gt;call&gt;](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#call) для запуска функции и поэтому также принимает строку обратного вызова.

#### esc <a id="esc"></a>

**Кодируйте символы в эквивалентные HTML сущности**

```text
string esc ( mixed $arg )
```

Использование:

```text
echo $view->esc("99 bottles of <b>beer</b> on the wall. <script>alert(1);</script>");
// 99 bottles of &amp;lt;b&amp;gt;beer&amp;lt;/b&amp;gt; on the wall. &amp;lt;script&amp;gt;alert(1);&amp;lt;/script&amp;gt;
```

Это также работает с массивами и свойствами объектов:

```text
$myArray = array('<b>foo</b>',array('<script>alert(1)</script>'),'key'=>'<i>foo</i>');
print_r($view->esc($myArray));
/*
    [0] => &amp;lt;b&amp;gt;foo&amp;lt;/b&amp;gt;
    [1] => Array
        (
            [0] => &amp;lt;script&amp;gt;alert(1)&amp;lt;/script&amp;gt;
        )
    [key] => &amp;lt;i&amp;gt;foo&amp;lt;/i&amp;gt;
*/

$myObj = new stdClass();
$myObj->title = '<h1>Hello World</h1>';
var_dump($view->esc($myObj));
/*
    object(stdClass)#23 (1) {
      ["title"] => string(32) "&amp;lt;h1&amp;gt;Hello World&amp;lt;/h1&amp;gt;"
    }
*/
```

Если системный VAR **ESCAPE** включен \(это по умолчанию\), то каждый доступ к ключу улья с использованием шаблонного токена типа **{{@myContent}}** автоматически экранируется этой функцией. Если это нежелательно, загляните в раздел [шаблонов](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/template) для дальнейших постпроцессоров.

#### сырой <a id="raw"></a>

**Декодирование HTML сущностей в эквивалентные символы**

```text
string raw ( mixed $arg )
```

Пример:

```text
$view->raw("99 bottles of &amp;lt;b&amp;gt;beer&amp;lt;/b&amp;gt; on the wall. &amp;lt;script&amp;gt;alert(1);&amp;lt;/script&amp;gt;");
// 99 bottles of <b>beer</b> on the wall. <script>alert(1);</script>
```

Этот метод также обрабатывает вложенные элементы массива и свойства объектов, как `$view->esc()`и другие.

