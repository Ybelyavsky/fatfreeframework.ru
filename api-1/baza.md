# База

Базовый класс представляет ядро фреймворка. Он содержит все необходимое для запуска простого приложения. Файл `base.php`также включает в себя основные [кэш](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/cache), [сборку](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/prefab-registry), [просмотр](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/view), [ISO](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/iso) и классы реестра, чтобы уменьшить ненужный дисковый ввод-вывод для оптимальной производительности.

Не стесняйтесь удалять все остальные файлы в `lib/`каталоге, если все, что вам нужно, - это основные функции, предоставляемые этим пакетом.

Пространство имен: `\`  
Расположение файла: `lib/base.php`

### улей <a id="the-hive"></a>

Улей-это массив памяти, в котором хранятся переменные фреймворка в виде пар ключ / значение. Хранение значения в улье гарантирует, что оно глобально доступно для всех классов и методов в вашем приложении.

#### набор <a id="set"></a>

**Привязка значения к ключу улья**

```text
mixed set ( string $key, mixed $val [, int $ttl = 0 ] )
```

_Примеры установки переменных фреймворка:_

```text
$f3->set('a',123); // a=123, integer
$f3->set('b','c'); // b='c', string
$f3->set('c','whatever'); // c='whatever', string
$f3->set('d',TRUE); // d=TRUE, boolean
```

_Установка массивов:_

```text
$f3->set('hash',array( 'x'=>1,'y'=>2,'z'=>3 ) );
// dot notation is also possible:
$f3->set('hash.x',1);
$f3->set('hash.y',2);
$f3->set('hash.z',3);
```

_Настройка свойств объекта:_

```text
$f3->set('a',new \stdClass);
$f3->set('a->hello','world');
echo $f3->get('a')->hello; // world
```

_более короткий синтаксис ArrayAccess, начиная с версии v3. 4. 0_

```text
$f3->LANGUAGE = 'en';
$f3->foo = 1234;
$f3['bar'] = 'buzzword';
```

**Свойства кэширования**

Если `$ttl`параметр равен &gt; 0 и [механизм кэширования &gt; framework](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/cache) включен, указанная переменная будет кэшироваться в течение `$ttl`нескольких секунд. Уже кэшированные Вары будут обновлены путем повторного использования старого времени истечения срока действия.

Если вам нужно кэшировать vars в течение бесконечного времени, проверьте метод [Cache-&gt;set&gt;](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/cache#set).

Вы можете кэшировать строки, массивы и все другие типы-даже полные объекты. `get()` будет автоматически загружать их из кэша.

_Примеры кэширования фреймворковых переменных:_

```text
// cache string
$f3->set('simplevar','foo bar 1337', 3600); // cache for 1 hour
//cache big computed arrays
$f3->set('fruits',array(
    'apple',
    'banana',
    'peach',
), 3600);
// cache objects
$f3->set('myClass1', new myClass('arg1'), 3600);
// change expire time for a single cookie var
$f3->set('COOKIE.foo', 123, 3600);  // 1 hour
$f3->set('COOKIE.bar', 456, 86400); // 1 day
```

**Системная переменная**

Фреймворк имеет свои собственные [системные переменные](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/quick-reference#system-variables). Вы можете изменить их, чтобы изменить поведение фреймворка, например:

```text
$f3->set('CACHE', TRUE);
$f3->set('HALT', FALSE);
$f3->set('CASELESS', FALSE);
```

Также можно установить PHP глобалы через [файлы COOKIE F3, GET, POST, REQUEST, SESSION, FILES, SERVER, env системные переменные](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/quick-reference#cookie,-get,-post,-request,-session,-files,-server,-env). Эти 8 переменных автоматически синхронизируются с базовыми глобалами PHP.**Примечание:** Если вы устанавливаете или получаете доступ к ключу сеанса, сеанс запускается автоматически. Вам нет необходимости делать это самостоятельно.

**Помните**: ключи улья _чувствительны к регистру_.  
Кроме того, ключи корневого улья проверяются на валидность по этим разрешенным символам: \[ a-z A-Z 0-9 \_ \]

#### получить <a id="get"></a>

**Извлечение содержимого ключа улья**

```text
mixed get ( string $key [, string|array $args = NULL ] )
```

Чтобы получить значение ранее сохраненного фреймворка var, используйте:

```text
$f3->set('myVar','hello world');
echo $f3->get('myVar'); // outputs the string 'hello world'
$local_var = $f3->get('myVar'); // $local_var holds the string 'hello world'
```

Если возвращаемое значение представляет собой строку, содержащую один или несколько [заполнителей формата](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#format), аргументы могут передаваться непосредственно после имени ключа:

```text
$f3->set('var1','Current date: {0,date} - Current time: {0,time}');
$f3->set('var2','Departure: {0,date} - Arrival: {1,date}');
echo $f3->get('var1',time());//shorthand for $f3->format($f3->get('var1'),time());
echo $f3->get('var2',array($timestamp1,$timestamp2));//shorthand for $f3->format($f3->get('var2'),$timestamp1,$timestamp2);
```

**Обратите внимание:** если кэширование включено и var ранее не был определен во время выполнения, F3 пытается загрузить var из кэша при использовании get\(\).

Доступ к массивам очень прост. Вы также можете использовать точечную нотацию JS`'myarray.bar'`, что значительно облегчает чтение и запись.

```text
$f3->set('myarray',
           array(
                    0 => 'value_0',
                    1 => 'value_1',
                'bar' => 123,
                'foo' => 'we like candy',
                'baz' => 4.56,
                )
         );

echo $f3->get('myarray[0]'); // value_0
echo $f3->get('myarray.1'); // value_1
echo $f3->get('myarray.bar'); // 123
echo $f3->get('myarray["foo"]'); // we like candy
echo $f3->get('myarray[baz]'); // 4.56, notice alternate use of single, double and no quotes
// a new ArrayAccess syntax is also possible since v3.4.0
echo $f3->myarray['foo']; 
echo $f3['myarray']['baz'];
```

#### синхронизация <a id="sync"></a>

**Синхронизируйте глобальную переменную PHP с соответствующим ключом улья**

```text
array sync ( string $key )
```

Использование:

```text
$f3->sync('SESSION'); // ensures PHP global var SESSION is the same as F3 variable SESSION
```

F3 автоматически синхронизирует следующие PHP глобалы: **GET**, **POST**, **COOKIE**, **REQUEST**, **SESSION**, **FILES**, **SERVER**, **ENV**

#### ссылка <a id="ref"></a>

**Получить ссылку на ключ улья и его содержимое**

```text
mixed &ref ( string $key [, bool $add = true, mixed $var = null ] )
```

Использование:

```text
$f3->set('name','John');
$b = &$f3->ref('name'); // $b is a reference to framework variable 'name' , not a copy
$b = 'Chuck'; // modifiying the reference updates the framework variable 'name'
echo $f3->get('name'); // Chuck
```

Вы также можете добавить несуществующие ключи улья, элементы массива и свойства объекта, если 2-й аргумент по умолчанию имеет значение TRUE.

```text
$new = &$f3->ref('newVar'); // creates new framework hive var 'newVar' and returns reference to it
$new = 'hello world'; // set value of php variable, also updates reference
echo $f3->get('newVar'); // hello world

$new = &$f3->ref('newObj->name');
$new = 'Sheldon';
echo $f3->get('newObj')->name; // Sheldon
echo $f3->get('newObj->name'); // Sheldon
echo $f3->get('newObj.name'); // Sheldon

$a = &$f3->ref('hero.name');
$a = 'SpongeBob';
// or
$b = &$f3->ref('hero'); // variable
$b['name'] = 'SpongeBob';  // becomes array with key 'name'
$my_array = $f3->get('hero');
echo $my_array['name']; // 'SpongeBob'
```

Если 2-й аргумент `$add`равен`false`, то он просто возвращает содержимое ключа улья только для чтения. Это поведение используется get \(\). Если ключ улья не существует, он возвращает значение NULL.

Используйте 3-й аргумент, если вы хотите найти ссылку в своем собственном массиве / объекте, а не в улье.

```text
$fruitQty = ["Bananas"=>5, "Oranges"=>2, "Apples"=>42, "Mangos"=>1];
$apples = &$f3->ref("Apples", true, $fruitQty); // References $fruitQty["Apples"]
$apples = 10;

echo $fruitQty["Apples"]; // 10
```

Таким образом, вы также можете использовать точечную нотацию с вашими собственными объектами.

#### существует <a id="exists"></a>

**Возвращает TRUE, если ключ улья установлен \(или возвращает метку времени и TTL, если кэширован\)**

```text
bool exists ( string $key [, mixed &$val=NULL] )
```

Эта `exists`функция также проверяет внутреннее хранилище кэша, когда ключ не найден в улье. Если ключ найден в кэше, он затем возвращается `array ( $timestamp, $ttl )`.**Обратите внимание:** exists использует функцию PHP \`isset \(\)\`, чтобы определить, установлен ли ключ улья и не равен нулю.

Использование:

```text
$f3->set('foo','value');

$f3->exists('foo'); // true
$f3->exists('bar'); // false, was not set above
```

`exists` это особенно полезно с [глобальными переменными PHP автоматически синхронизируемыми F3](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#sync):

```text
// Synched hive keys with PHP global variables
$f3->exists('COOKIE.userid');
$f3->exists('SESSION.login');
$f3->exists('POST.submit');
```

**Обратите внимание:** если вы проверяете наличие ключа сеанса, сеанс запускается автоматически.

`$val`Этот аргумент можно также использовать для извлечения содержимого ключа hive. Это может сэкономить дополнительный `get`звонок.

```text
if ($f3->exists('foo',$value)) {
    echo $value; // bar
}
```

#### лишенный <a id="devoid"></a>

**Возвращает TRUE, если ключ hive пуст и не кэшируется**

```text
bool devoid ( string $key )
```

Эта `devoid`функция также проверяет внутреннее хранилище кэша, если ключ не был найден в улье.**Обратите внимание:** devoid использует функцию PHP \`empty \(\)\`, чтобы определить, является ли ключ hive пустым и не кэшируется.

Использование:

```text
$f3->set('foo','');
$f3->set('bar',array());
$f3->set('baz',array(),10);

$f3->devoid('foo'); // true
$f3->devoid('bar'); // true
$f3->devoid('baz'); // false
```

#### Очистить <a id="clear"></a>

**Unset Hive key, ключ больше не существует**

```text
void clear ( string $key )
```

Чтобы полностью удалить ключ улья и его значение из памяти:

```text
$f3->clear('foobar');
$f3->clear('myArray.param1'); // removes key `param1` from array `myArray`
```

Если данный ключ улья был кэширован ранее, он также будет очищен от кэша.

Еще несколько специальных применений:

```text
$f3->clear('SESSION'); // destroys the user SESSION
$f3->clear('COOKIE.foobar'); // removes a cookie
$f3->clear('CACHE'); // clears all cache contents
```

**Обратите внимание:** очистка всего содержимого кэша сразу не поддерживается для бэкенда кэша XCache

#### mset <a id="mset"></a>

**Многопеременное присвоение с использованием ассоциативного массива**

```text
void mset ( array $vars [, string $prefix = '' [, integer $ttl = 0 ]] )
```

Использование:

```text
$f3->mset(
    array(
        'var1'=>'value1',
        'var2'=>'value2',
        'var3'=>'value3',
    )
);

echo $f3->get('var1'); // value1
echo $f3->get('var2'); // value2
echo $f3->get('var3'); // value3
```

Вы можете добавить все имена ключей, используя 2-й аргумент `$prefix`.

```text
$f3->mset(
    array(
        'var1'=>'value1',
        'var2'=>'value2',
        'var3'=>'value3',
    ),
    'pre_'
);

echo $f3->get('pre_var1'); // value1
echo $f3->get('pre_var2'); // value2
echo $f3->get('pre_var3'); // value3
```

Чтобы кэшировать все Вары, установите положительное числовое целое значение `$ttl`в секундах.

#### улей <a id="hive"></a>

**Возвращает все содержимое Улья в виде массива**

```text
array hive ()
```

Использование:

```text
printf ("A Busy Hive: <pre>%s</pre>", var_export( $f3->hive(), true ) );
```

#### Копировать <a id="copy"></a>

**Скопируйте содержимое переменной hive в другую переменную**

```text
mixed copy ( string $src, string $dst )
```

Возвращает доступную для записи ссылку на новый `$dst`ключ улья. Если `$dst`он уже существует в улье, он просто перезаписывается.

Использование:

```text
$f3->set('foo','123');
$f3->set('bar','barbar');
$bar = $f3->copy('foo','bar'); // bar = '123'
$bar = 456;
$f3->set('foo','789');
echo $f3->get('bar'); // '456'
```

#### конкат <a id="concat"></a>

**Связать строку с переменной Hive string**

```text
string concat ( string $key, string $val )
```

Возвращает результат конкатенации. **Примечание**: если `$key`он не существует в улье, то он автоматически создается в улье.

Использование:

```text
$f3->set('count', 99);
$f3->set('item', 'beer');
$text = $f3->concat('count', ' bottles of '.$f3->get('item'));
$text .= ' on the wall';
$f3->concat('wall', $f3->get('count')); // new 'wall' hive key is created
echo $f3->get('wall'); // 99 bottles of beer on the wall
```

#### сальто <a id="flip"></a>

**Ключи подкачки и значения переменной массива улья**

```text
array flip ( string $key )
```

Использование:

```text
$f3->set('data', array(
    'foo1' => 'bar1',
    'foo2 '=> 'bar2',
    'foo3' => 'bar3',
));
$f3->flip('data');

print_r($f3->get('data'));
/* output:
Array
(
    [bar1] => foo1
    [bar2] => foo2
    [bar3] => foo3
)
*/
```

#### толкать <a id="push"></a>

**Добавить элемент в конец переменной массива улья**

```text
mixed push ( string $key, mixed $val )
```

Использование:

```text
$f3->set('fruits',array(
    'apple',
    'banana',
    'peach',
));
$f3->push('fruits','cherry');

print_r($f3->get('fruits'));
/* output:
Array
(
    [0] => apple
    [1] => banana
    [2] => peach
    [3] => cherry
)
*/
```

#### хлопок <a id="pop"></a>

**Удалить последний элемент переменной массива улья**

```text
mixed pop ( string $key )
```

Использование:

```text
$f3->set('fruits',array(
	'apple',
	'banana',
	'peach'
));
$f3->pop('fruits'); // returns "peach"

print_r($f3->get('fruits'));
/* output:
Array
(
    [1] => apple
    [2] => banana
)
*/
```

#### unshift <a id="unshift"></a>

**Добавить элемент в начало переменной массива hive**

```text
mixed unshift ( string $key, string $val )
```

Использование:

```text
$f3->set('fruits',array(
	'apple',
	'banana',
	'peach'
));
$f3->unshift('fruits','cherry');

print_r($f3->get('fruits'));
/* output:
Array
(
    [0] => cherry
    [1] => apple
    [2] => banana
    [3] => peach
)
*/
```

#### сдвиг <a id="shift"></a>

**Удалить первый элемент переменной массива улья**

```text
array|NULL shift ( string $key )
```

Возвращает переменную массива улья, сдвинутую влево, или `NULL`если переменная массива улья пуста или не является массивом.**Обратите**внимание: `shift`используйте функцию PHP `array_shift()`. Это означает, что все числовые ключи массива переменной Hive array будут изменены, чтобы начать отсчет с нуля, в то время как литеральные ключи не будут затронуты

Пример:

```text
$f3->set('fruits', array(
    'crunchy'=>'apples',
    '11'=>'bananas',
    '6'=>'kiwis',
    'juicy'=>'peaches'
));
$f3->shift('fruits'); // returns "apples"
print_r($f3->get('fruits'));
/* output:
Array
(
    [0] => bananas
    [1] => kiwis
    [juicy] => peaches
)
*/
```

#### поглощать <a id="merge"></a>

**Объединить массив с переменной массива улья**

```text
array merge ( string $key, array $src [, bool $keep = FALSE])
```

Возвращает результирующий массив слияния. \(Не касается значения ключа улья\)

Пример:

```text
$f3->set('foo', array('blue','green'));
$bar = $f3->merge('foo', array('red', 'yellow'));

/* $bar now is:
array (size=4)
  [0] => string 'blue' (length=4)
  [1] => string 'green' (length=5)
  [2] => string 'red' (length=3)
  [3] => string 'yellow' (length=6)
*/
```

Если параметр `$keep`имеет _значение TRUE_, источник `$key`в улье также обновляется.

#### расширять <a id="extend"></a>

**Расширение переменной массива hive со значениями по умолчанию из $src**

```text
array extend ( string $key, array $src [, bool $keep = FALSE])
```

Этот метод предоставляет простой способ добавить некоторые значения по умолчанию в массив:

```text
$f3->set('settings',[
	'foo'=> 1,
	'bar'=> 'baz',
	'colors'=>[
		'blue'=>1,
		'green'=>2
	]
]);
$f3->set('defaults',[
	'foo'=>0,
	'zzz'=>2,
	'colors'=>[
		'red'=>3,
		'blue'=>4
	],
]);
$settings = $f3->extend('settings','defaults');
print_r($settings);

/*
Array (
    [foo] => 1
    [zzz] => 2
    [colors] => Array (
            [red] => 3
            [blue] => 1
            [green] => 2
        )
    [bar] => baz
)
*/
```

Если параметр `$keep`имеет _значение TRUE_, источник `$key`в улье также обновляется.

### Кодирование И Преобразование <a id="encoding-conversion"></a>

#### fixslashes <a id="fixslashes"></a>

**Преобразование обратной косой черты в косую черту**

```text
string fixslashes ( string $str )
```

Использование:

```text
$filepath = __FILE__; // \www\mysite\myfile.txt
$filepath = $f3->fixslashes($filepath); // /www/mysite/myfile.txt
```

#### расщеплять <a id="split"></a>

**Разделенная запятая, точка с запятой или разделенная трубой строка**

```text
array split ( string $str [, $noempty = TRUE ] )
```

Использование:

```text
$data = 'value1,value2;value3|value4';
print_r($f3->split($data));
/* output:
Array
(
    [0] => value1
    [1] => value2
    [2] => value3
    [3] => value4
)
*/
```

NB: по умолчанию пустые значения отфильтровываются. Установите `$noempty`значение FALSE, чтобы предотвратить такое поведение.

#### stringify <a id="stringify"></a>

**Преобразование PHP-выражения/значения в сжатую экспортируемую строку**

```text
string stringify ( mixed $arg [, array $stack = NULL ] )
```

Эта функция позволяет преобразовать любое выражение PHP, значение, массив или любой объект в сжатую и экспортируемую строку.

Этот `$detail`параметр определяет, следует ли рекурсивно входить во вложенные объекты или нет.

Пример с простым 2D массивом:

```text
$elements = array('water','earth','wind','fire');
$fireworks = array($elements, shuffle($elements), array_flip($elements));
echo $f3->stringify($fireworks);
// Outputs e.g.:
"[['water','earth','wind','fire'],true,['earth'=>0,'water'=>1,'wind'=>2,'fire'=>3]]"
```

```text
$car = new stdClass();
$car->color = 'green';
$car->location = array('35.360496','138.727798');
echo $f3->stringify($car);
// Outputs e.g.:
"stdClass::__set_state(['color'=>'green','location'=>['35.360496','138.727798']])"
```

#### csv <a id="csv"></a>

**Сглаживайте значения массива и возвращайте их в виде строки CSV**

```text
string csv ( array $args )
```

Использование:

```text
$elements = array('water','earth','wind','fire');
echo $f3->csv($elements); // displays 'water','earth','wind','fire' // including single quotes
```

#### верблюжий футляр <a id="camelcase"></a>

**Преобразование строки snake\_case в camelCase**

```text
string camelcase ( string $str )
```

Использование:

```text
$str_s_c = 'user_name';
$f3->camelcase($str_s_c); // returns 'userName'
```

#### змеиная кожа <a id="snakecase"></a>

**Преобразование строки camelCase в snake\_case**

```text
string snakecase ( string $str )
```

Использование:

```text
$str_CC = 'userName';
$f3->snakecase($str_CC); // returns 'user_name'
```

#### знак <a id="sign"></a>

**Возвращает -1, если указанное число отрицательно, 0, если ноль, или 1, если число положительное**

```text
int sign ( mixed $num )
```

#### гашиш <a id="hash"></a>

**Генерация 64-битного/базового хэша 36**

```text
string hash ( string $str )
```

Генерирует хэш для заданной строки \(длина от 11 до 13\)

Пример:

```text
$f3->hash('foobar'); // returns '3vrllw03cko4s' (length=13)
```

#### base64 <a id="base64"></a>

**Возврат эквивалента в кодировке Base64**

```text
string base64 ( string $data, string $mime )
```

Пример:

```text
echo $f3->base64('<h1>foobar</h1>','text/html');
// data:text/html;base64,PGgxPmZvb2JhcjwvaDE+
```

#### зашифровать <a id="encode"></a>

**Преобразование специальных символов в HTML-объекты**

```text
string encode ( string $str )
```

Кодирует такие символы, как `& < >`и другие символы, основываясь на настройках кодирования ваших приложений. \(по умолчанию: UTF-8\)

Пример:

```text
echo $f3->encode("we <b>want</b> 'sugar & candy'");
// we &lt;b&gt;want&lt;/b&gt; 'sugar &amp; candy'
```

#### расшифровывать <a id="decode"></a>

**Преобразование специальных HTML-сущностей обратно в символы**

```text
string decode ( string $str )
```

Пример:

```text
echo $f3->decode("we &lt;b&gt;want&lt;/b&gt; 'sugar &amp; candy'");
// we <b>want</b> 'sugar & candy'
```

#### чистый <a id="clean"></a>

**Удалите HTML-теги \(кроме перечисленных\) и непечатаемые символы, чтобы смягчить атаки XSS / code injection**

```text
string clean ( mixed $var [, string $tags = NULL ] )
```

`$var` может быть либо а`string`, либо Ан `array`. В последнем случае он будет рекурсивно пройден, чтобы очистить каждый элемент.

`$tags` определяет список [\(в соответствии с синтаксисом разделения\)](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#split) _разрешенных_ html-тегов, которые **не** будут удалены.**Совет**: рекомендуется использовать эту функцию для дезинфекции введенных формуляров.

Примеры:

```text
$foo = "99 bottles of <b>beer</b> on the wall. <script>alert('scripty!')</script>";
echo $f3->clean($foo); // "99 bottles of beer on the wall. alert('scripty!')"
```

```text
$foo = "<h1><b>nice</b> <span>news</span> article <em>headline</em></h1>";
$h1 = $f3->clean($foo,'h1,span'); // <h1>nice <span>news</span> article headline</h1>
```

#### скраб <a id="scrub"></a>

**Аналогично clean \(\), за исключением того, что переменная передается по ссылке**

```text
string scrub ( mixed &$var [, string $tags = NULL ] )
```

Пример:

```text
$foo = "99 bottles of <b>beer</b> on the wall. <script>alert('scripty!')</script>";
$bar = $f3->scrub($foo);
echo $foo; // 99 bottles of beer on the wall. alert('scripty!')
```

#### сериализовать <a id="serialize"></a>

**Возвращаемое строковое представление значения PHP**

```text
string serialize ( mixed $arg )
```

В зависимости от системной переменной сериализатора этот метод преобразует что-либо в переносимое строковое выражение. Возможные значения-**igbinary** и **php**. F3 проверяет при запуске, доступен ли igbinary, и определяет его приоритетность, так как расширение igbinary работает намного быстрее и использует меньше дискового пространства для сериализации. Проверьте [igbinary на github](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/github.com/igbinary/igbinary).

#### несериализовать <a id="unserialize"></a>

**Возвращает значение PHP, полученное из строки**

```text
string unserialize ( mixed $arg )
```

См[. раздел сериализация](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#serialize) для дальнейшего описания.

### Локализация <a id="localisation"></a>

**Примечание:** F3 следует [правилам форматирования ICU](https://z5h64q92x9.net/proxy_u/en-ru.ru/userguide.icu-project.org/formatparse/) **, не** требуя наличия модуля PHP intl.

#### формат <a id="format"></a>

**Возвращаемая форматированная строка с учетом локали**

```text
string format( string $format [, mixed $arg0 [, mixed $arg1...]] )
```

`$format`Строка содержит один или несколько заполнителей, идентифицируемых индексом позиции, заключенным в фигурные скобки, причем начальный индекс равен 0. Заполнители заменяются значениями предоставленных аргументов.

```text
echo $f3->format('Name: {0} - Age: {1}','John',23); //outputs the string 'Name: John - Age: 23'
```

Форматирование может стать более точным, если ожидаемый тип указан в заполнителях.

В настоящее время поддерживаются следующие типы:

* Дата
* дата, короткая
* дата, длинная
* дата, обычай, {exp}
* время
* время, короткое
* время, обычай, {exp}
* число, целое число
* число, валюта
* число, валюта, int
* число, валюта, {char}
* число, в процентах
* число, десятичное число, {int}
* множественное число, {exp}

```text
echo $f3->format('Current date: {0,date} - Current time: {0,time}',time());
//outputs the string 'Current date: 04/12/2013 - Current time: 11:49:57'
echo $f3->format('Created on: {0,date,custom,%A, week: %V }', time());
//outputs the string 'Created on: Monday, week 45'
echo $f3->format('{0} is displayed as a decimal number while {0,number,integer} is rounded',12.54);
//outputs the string '12.54 is displayed as a decimal number while 13 is rounded'
echo $f3->format('Price: {0,number,currency}',29.90);
//outputs the string 'Price: $29.90'
echo $f3->format('Percentage: {0,number,percent}',0.175);
//outputs the string 'Percentage: 18%' //Note that the percentage is rendered as an integer
echo $f3->format('Decimal Number: {0,number,decimal,2}', 0.171231235);
//outputs the string 'Decimal Number: 0,17'
```

Синтаксис **множественного** числа немного менее прост, так как он позволяет связать различные выходные данные в зависимости от количества входных данных.

Синтаксис множественного типа должен `0, plural,`начинаться со списка ключевых слов множественного числа, связанных с требуемым результатом. Допустимые ключевые слова: "\* ноль\*", " \* один\*", "\* два \* " и " \*другой\*".

Кроме того, вы можете вставить соответствующую цифру в выходные строки благодаря `#`знаку, который будет автоматически заменен соответствующим числом, как показано в примере ниже:

```text
$cart_dialogs= '{0, plural,'.
	'zero	{Your cart is empty.},'.
	'one	{One (#) item in your cart.},'.
	'two	{A pair of items in your cart.},'.
	'other	{There are # items in your cart.}
}';

echo $f3->format($cart_dialogs,0); // displays 'Your cart is empty.'
echo $f3->format($cart_dialogs,1); // displays 'One (1) item in your cart.'
echo $f3->format($cart_dialogs,2); // displays 'A pair of items in your cart.'
echo $f3->format($cart_dialogs,3); // displays 'There are 3 items in your cart.'
```

Каждое ключевое слово множественного числа является необязательным, и вы можете, например, опустить ключевое слово множественного числа "два", если "другой" подходит для этого случая. Конечно, если вы опустите их все, то будут отображаться только цифры. Как правило, держите хотя бы ключевое слово "другое" во множественном числе в качестве запасного варианта.

**Автоматическое форматирование переменных ульяПриятно помнить:** F3, для вашего удобства и для того, чтобы чрезвычайно облегчить использование форматирования в ваших шаблонах, вы можете извлекать и форматировать переменные из улья с помощью одной команды:

Пример:

```text
$f3->set('a_books_story',
	'{0, plural,'.
		'zero	{There is n#thing on the table.},'.
		'one	{A book is on the table.},'.
		'two	{Two (#) books are on this table.},'.
		'other	{There are # books on the table.}'.
	'}'
);
echo $f3->get('a_books_story',0); // displays 'There is n0thing on the table.'
echo $f3->get('a_books_story',1); // displays 'A book is on the table.'
echo $f3->get('a_books_story',2); // displays 'Two (2) books are on this table.'
echo $f3->get('a_books_story',7); // displays 'There are 7 books on the table.'
```

#### язык <a id="language"></a>

**Назначение / автоматическое определение языка**

```text
string language ( string $code )
```

Эта функция используется при загрузке фреймворка для автоматического определения возможного языка пользователя, просматривая заголовки HTTP-запросов, в частности заголовок Accept-Language, отправляемый браузером.

**Используйте системную переменную LANGUAGE для получения и установки языков**, так как она также обрабатывает зависимости, такие как установка локалей с помощью [php setlocale\(LC\_ALL,...\)](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/php.net/manual/en/function.setlocale.php) и изменение файлов словаря. [Резервная](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/quick-reference#fallback) системная переменная определяет язык по умолчанию, который будет использоваться, если ни один из обнаруженных языков не доступен в виде файла словаря.

Пример:

```text
$f3->get('LANGUAGE'); // 'de-DE,de,en-US,en'
$f3->set('LANGUAGE', 'es-BR,es');
$f3->get('LANGUAGE'); // 'es-BR,es,en' the fallback language is added at the end of the list
```

Языковая переменная принимает тот же тип строки, что и заголовок HTTP Accept-Language, который представляет собой разделенный запятыми список 2-буквенных кодов языков, за которыми, возможно, следуют **дефис** и 2-буквенный код страны.  
  
**NB:** несмотря на то, что локали POSIX используют подчеркивание в качестве разделителя \(es\_BR.UTF-8\), вы должны определить переменную языка с дефисом \(es-BR\). Файлы словарей следуют тому же правилу \(es-BR.php / es-BR.ini\).

**Язык системы**

Чтобы гарантировать, что метод [format](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#format) и другие функции php, ориентированные на локализацию, работают так, как ожидалось, эта функция автоматически загружает системную локализацию. Например:

```text
$f3->set('ENCODING','UTF-8');
$f3->set('LANGUAGE','it-IT');// the locale it_IT.UTF-8 will be automatically loaded using setlocale
```

Еще несколько примеров:

```text
$f3->LANGUAGE='en' => F3 will try to load 2 locales:
    en.UTF-8
    en
$f3->LANGUAGE='en-US' => F3 will try to load 4 locales:
    en_US.UTF-8
    en_US
    en.UTF-8
    en
$f3->LANGUAGE='en-US,en-GB' => F3 will try to load 6 locales:
    en_US.UTF-8
    en_US
    en_GB.UTF-8
    en_GB
    en.UTF-8
    en
```

**NB:** загружается первая локаль, найденная на сервере. Убедитесь, что на вашем сервере установлены все локали, которые вы хотите поддерживать в своем приложении. На большинстве машин linux вы можете проверить это с помощью `locale -a`и установить новые локали `dpkg-reconfigure locales`. Некоторые конфигурации веб-серверов apache, возможно, нуждаются в перезапуске после этого, чтобы работать с новыми локалями.

#### лексикон <a id="lexicon"></a>

**Перенос записей лексикона в улей**

```text
array lexicon ( string $path )
```

Эта функция используется при загрузке фреймворка для автоматической загрузки файлов словаря, расположенных в пределах определенного пути в `LOCALES`var.

```text
$f3->set('LOCALES','dict/');
```

Файл словаря может быть php-файлом, возвращающим парный ассоциативный массив ключ-значение, или an .конфигурационный файл в формате ini-style. [Ознакомьтесь с руководством по языковым файлам здесь](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/views-and-templates#multilingual-support).

### Маршрутизация <a id="routing"></a>

#### псевдоним <a id="alias"></a>

**Соберите url-адрес из псевдонима**

```text
string alias ( string $name [, array|string $params = array() [, array|string $query = array() ]] )
```

Пример:

```text
$f3->route('GET @complex:/resize/@format/*/sep/*','App->nowhere');
$f3->alias('complex','format=20x20,2=foo/bar,3=baz.gif');
$f3->alias('complex',array('format'=>'20x20',2=>'foo/bar',3=>'baz.gif'));
// Both examples return '/resize/20x20/foo/bar/sep/baz.gif'
```

#### строить <a id="build"></a>

**Замените маркированный URL-адрес значениями токенов текущего маршрута**

```text
string build ( string $url [, array $params = array() ] )
```

Пример:

```text
// for instance the route is '/subscribe/@channel' with PARAMS.channel = 'fatfree'

echo $f3->build('@channel'); // displays 'fatfree'
echo $f3->build('/get-it/now/@channel'); // displays '/get-it/now/fatfree'
echo $f3->build('/subscribe/@channel');  // displays '/subscribe/fatfree'
```

Если вы хотите конкретно определить маркеры в данном `$url`случае , вы можете использовать `$params`аргумент для этого:

```text
$f3->build('/resize/@format/*/sep/*',array(
  'format'=>'200x200',// 1=>'200x200' also works
  2=>'foo/bar',
  3=>'baz.gif'
));
// returns: /resize/200x200/foo/bar/sep/baz.gif
```

#### издеваться <a id="mock"></a>

**Имитация HTTP-запроса**

```text
mixed mock ( string $pattern [, array $args = NULL [, array $headers = NULL [, string $body = NULL ]]] )
```

Это эмулирует HTTP-запрос, основанный на глаголе и ресурсе, определенном`$pattern`.

* `$args` экспортируется как глобальная переменная соответствующего глагола \(`$_GET`, `$_POST`или`$_REQUEST`\)
* `$headers` экспортируется как глобальные HTTP заголовки \(`$_SERVER[HTTP_…]`\)
* Тело HTTP-запроса `$body`экспортируется как `BODY`переменная улья для глаголов, не равных `GET`or `HEAD`. If `$body`не определен, `$args`получает URL-кодировку и экспортируется как `BODY`
* Добавление `[ajax]`к `$pattern`издевательствам над вызовами AJAX
* Добавление `[sync]`к `$pattern`издевательствам обычных \(синхронных\) вызовов
* Добавление `[cli]`к `$pattern`вызовам командной строки mocks \(CLI\)
* [Именованные маршруты и токены](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/routing-engine#NamedRoutes) являются допустимыми ресурсами

Базовый пример использования:

```text
$f3->mock('GET /page/view [ajax]');
```

Базовый пример использования с именованным маршрутом и токеном:

```text
$f3->route('GET @grub:/food/@id', /* … */);
$f3->mock('GET @grub(@id=bread)');
```

[Модульный тест](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/unit-testing) как пример расширенного использования:

```text
$f3->route('GET|POST|PUT @grub:/food/@id/@quantity', /* … */);
$f3->mock('POST /food/sushki/134?a=1',array('b'=>2));
$test->expect(
	$_GET==array('a'=>1) && $_POST==array('b'=>2) && $_REQUEST==array('a'=>1,'b'=>2) && $f3->get('BODY')=='b=2',
	'Request body and superglobals $_GET, $_POST, $_REQUEST correctly set on mocked POST'
);
```

#### разбор <a id="parse"></a>

**Проанализируйте строку, содержащую пары ключ-значение, и используйте их в качестве маркеров маршрутизации**

```text
NULL parse ( string $str )
```

Пример:

```text
$f3->parse('framework=f3 , speed=fast , features=full');
echo $f3->get('PARAMS.framework');  // 'f3'
echo $f3->get('PARAMS.speed');      // 'fast'
```

#### маршрут <a id="route"></a>

**Привязать шаблон маршрута к заданному обработчику**

```text
null route ( string|array $pattern, callback $handler [, int $ttl = 0 [, int $kbps = 0 ]] )
```

Базовый пример использования:

```text
$f3->route('POST /login','AuthController->login');
```

**Схема Маршрута**

`$pattern`Var описывает шаблон маршрута, состоящий из типа запроса и URI запроса, разделенных пробелом char.

**Глаголы**

Возможные определения типа запроса \(глагола\), которые будет обрабатывать F3 , следующие: **GET**, **POST**, **PUT**, **DELETE**, **HEAD**, **PATCH**, **CONNECT**.

Вы можете объединить несколько глаголов, чтобы использовать один и тот же обработчик маршрута для всех них. Просто разделите их обуглившейся трубой, например `GET|POST`.

**Жетоны**

URI запроса может содержать один или несколько **маркеров**, предназначенных для определения динамических маршрутов. Маркеры обозначаются `@`символом перед их именем и могут быть дополнительно заключены в одиночные фигурные скобки `{ }`. Смотрите эти примеры:

```text
$f3->route('GET|HEAD /@page','PageController->display'); // ex: /about
$f3->route('POST /@category/@thread','ForumThread->saveAnswer'); // /games/battlefield3
$f3->route('GET /image/@width-@height/@file','ImageCompressor->render'); // /image/300-200/mario.jpg
$f3->route('GET /image/{@width}x{@height}/@file','ImageCompressor->render'); // /image/300x200/mario.jpg
```

После обработки URI входящего запроса \(инициированного [run](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#run)\) вы найдете значение каждого из этих токенов в `PARAMS`системной переменной в виде именованного ключа, например `$f3->get('PARAMS.file')`. /- Марио.jpg'**Обратите внимание:** маршруты и соответствующие им глаголы сгруппированы по их шаблону URL. Статические маршруты **предшествуют** маршрутам с динамическими маркерами или подстановочными знаками.

**Подстановочный знак**

Вы также можете определить подстановочные знаки \(`/*`\) в вашем URI маршрутизации. Кроме того, вы можете использовать их в сочетании с `@`токенами.

```text
$f3->route('GET /path/*/@page', function($f3,$params) {
    // called URI: "/path/cat/page1"
    // $params is the same as $f3->get('PARAMS');
    $params[0]; // contains the full route path. "/path/cat/page"
    $params[1]; // and further numeric keys in PARAMS hold the catched wildcard paths and tokens. in this case "cat"
    $params['page']; // is your last segment, in this case "page1"
});
```

**Обратите внимание:** var\` PARAMS ' содержит все токены в виде именованного ключа, а также все токены и подстановочные знаки в виде числового ключа, в зависимости от их порядка появления.

Приведенный выше маршрут также работает с подкатегориями. Просто позвонить `/path/cat/subcat/page1`

```text
$params[1]; // now contains "/cat/subcat"
```

Он даже работает с некоторыми другими подуровнями. Вам просто нужно разнести это значение с `/`помощью разделителя-для обработки ваших подкатегорий. Что-то вроде `/path/*/@pagetitle/@pagenum`этого тоже довольно просто.

Это становится сложным, когда вы пытаетесь использовать более одного подстановочного знака, потому что только первый `/*`подстановочный знак может содержать неограниченные сегменты пути. Любые дальнейшие подстановочные знаки могут содержать только одну часть между косыми чертами \(`/`\). Так что постарайтесь, чтобы все было просто.

**Группы**

Можно назначить несколько маршрутов одному и тому же обработчику маршрутов, используя массив маршрутов внутри `$pattern`. Это будет выглядеть так:

```text
$f3->route(
  array(
    'GET /archive',
    'GET /archive/@year',
    'GET /archive/@year/@month',
    'GET /archive/@year/@month/@day'
  ),
  function($f3,$params){
	$params+=array('year'=>2013,'month'=>1,'day'=>1); //default values
    //etc..
  }
);
```

**Именованные Маршруты**

Так `F3 v3.2.0`как вы также можете присвоить имя своим маршрутам. Поэтому следуйте этой схеме:

```text
$f3->route('GET @beer_list: /beer', 'Beer->list');
```

Имена вставляются после глагола маршрута и предваряются символом@. Все именованные маршруты могут быть доступны системной переменной ALIASES для дальнейшей обработки в шаблонах или для перенаправления. Дополнительную информацию см. В руководстве пользователя по [созданию именованных маршрутов](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/routing-engine#named-routes).

Кроме того вы можете использовать существующее имя маршрута для сокращения дополнительных определений маршрута:

```text
$f3->route('GET @beer_details: /beer/@id', 'Beer->get');
$f3->route('POST @beer_details', 'Beer->saveComment');
$f3->route('PUT @beer_details', 'Beer->savePhoto');
```

**обработчик маршрута**

Это может быть вызываемый метод класса, например 'Foo-&gt;bar' или 'Foo::bar', имя функции или анонимная функция.

F3 автоматически передает экземпляр фреймворка и маркеры маршрута классам контроллеров обработчиков маршрутов. Например:

```text
$f3->set('hello','world');
$f3->route('GET /foo/@file','Bar->baz');

class Bar {
    function baz($f3,$args) {//<-- $f3 is the framework instance, $args are the route tokens
        echo $f3->get('hello');
        echo $args['file'];
    }
}
```

**Кэширование**

3-й аргумент `$ttl`определяет время кэширования в секундах. Установка этого аргумента в положительное значение вызовет функцию [expire](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#expire) для установки метаданных кэша в заголовке HTTP-ответа. Он также кэширует ответ маршрута, но кэшируются только запросы GET и HEAD.

Если `CACHE`он выключен,`$ttl`то будет управлять кэшем браузера только с помощью метаданных заголовка expire. Если `CACHE`он включен и `$ttl`для текущего обработчика URI запроса установлено положительное значение, F3 дополнительно кэширует выходные данные для этой страницы и обновляет их по `$ttl`истечении срока действия. Подробнее об этом [читайте здесь](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/groups.google.com/d/msg/f3-framework/lwaqZjtwCvU/PC-gK8Ki9PMJ) и [здесь](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/groups.google.com/d/msg/f3-framework/lwaqZjtwCvU/LDUlPhQfc84J).

**Дросселирование Полосы Пропускания**

Установите 4-й аргумент `$kbps`на желаемое ограничение скорости, чтобы включить дросселирование. [Подробнее](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/optimization#bandwidth-throttling) об этом читайте в руководстве пользователя.

#### переадресовать <a id="reroute"></a>

**Перенаправление на указанный URI**

```text
null reroute ( [ string|array $url = NULL [, bool $permanent = FALSE [, bool $die = TRUE ]]] )
```

Пример использования:

```text
// an old page is moved permanently
$f3->route('GET|HEAD /obsoletepage', function($f3) {
    $f3->reroute('/newpage', true);
});

// whereas a Post/Redirect/Get pattern would just redirect temporarily
$f3->route('GET|HEAD /login', 'AuthController->viewLoginForm');
$f3->route('POST /login', function($f3) {
    if( AuthController->checkLogin == true )
        $f3->reroute('/members', false);
    else
        $f3->reroute('/login', false);
});

// if no $url parameter is set, it'll reroute to the current route with GET verb
$f3->route('POST /search', function($f3) {
    // back to search form, if an empty term was submitted
    if ($f3->devoid('POST.search_term'))
        $f3->reroute();
});

// we can also reroute to external URLs
$f3->route('GET /partners', function($f3) {
    $f3->reroute('http://externaldomain.com');
});

// it's also possible to reroute to named routes
$f3->route('GET @beer_list: /beer', 'Beer->list');
$f3->route('GET /old-beer-page', function($f3) {
    $f3->reroute('@beer_list');
});

// even with dynamic parameter in your named route
$f3->route('GET @beer_producers: /beer/@country/@village', 'Beer->byproducer');
$f3->route('GET /old-beer-page', function($f3) {
    $f3->reroute('@beer_producers(@country=Germany,@village=Rhine)');
});
// but it'll also work with any unnamed tokenized URL
$f3->reroute('/beer/@country/@village',TRUE)
```

Когда `$url`это массив, он используется для перенаправления псевдонимов. Вы можете указать имя псевдонима в 1-м элементе массива, его параметры во 2-м и дополнительные элементы запроса в качестве 3-го элемента массива:

```text
$f3->reroute(['filter','a=foo,b=bar','time=3']);
// or
$f3->reroute(['filter',['a'=>'foo','b'=>'bar'],['time'=>3]]);

// equivalent to:
$f3->reroute('@filter(a=foo,b=bar)?time=3');
```

Этот `$die`параметр можно использовать для отключения немедленного перенаправления, вместо этого скрипт продолжает работу после установки соответствующих заголовков. Это может быть полезно для модульного тестирования.

#### перенаправлять <a id="redirect"></a>

**Перенаправление маршрута на другой URL-адрес**

```text
null redirect ( string|array $pattern, string $url [, bool $permanent = TRUE ] )
```

Это небольшой метод быстрого доступа между _методами route_ и _reroute_ к определенным маршрутам, которые просто предназначены для перенаправления клиента. `$pattern`Аргумент принимает те же значения, что и метод маршрута. Ибо `$url`вы можете использовать все, что также будет принято в соответствующем аргументе метода reroute.

Пример использования:

```text
// redirect old pages
$f3->redirect('GET /oldpage', '/newpage');
// jump to absolut URLs
$f3->redirect('GET /external-link', 'http://subdomain.domain.com');
// temporarily redirect to another named route
$f3->redirect('GET /login', '@member_area', false);
// redirect one named route to another
$f3->redirect('GET @member_welcome', '@member_area');
```

Это также можно настроить в [файлах конфигурации](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/framework-variables#ConfigurationFiles) в пределах `[redirects]`раздела.

#### Карта <a id="map"></a>

**Предоставьте интерфейс ReST путем сопоставления команды HTTP методу класса**

```text
null map ( string $url, string $class [, int $ttl = 0 [, int $kbps = 0 ]] )
```

Его синтаксис работает немного похоже на функцию **маршрута**, но вам не нужно определять метод HTTP-запроса в 1-м аргументе, потому что они сопоставляются как функции в классе, который вы продуцируете в `$class`аргументе. [Подробнее о поддержке REST читайте в руководстве пользователя](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/routing-engine#rest-representational-state-transfer).

Пример использования:

```text
$f3->map('/news/@item','News');

class News {
    function get() {}
    function post() {}
    function put() {}
    function delete() {}
}
```

#### бежать <a id="run"></a>

**Сопоставьте маршруты с входящим URI и вызовите их обработчик маршрутов**

```text
null run ()
```

Пример использования:

```text
$f3 = require __DIR__.'/lib/base.php';
$f3->route('GET /',function(){
    echo "Hello World";
});
$f3->run();
```

После обработки URI входящего запроса шаблон маршрутизации, соответствующий этому URI, сохраняется в `PATTERN`var, текущий URI HTTP-запроса-в `URI`var, а метод запроса-в `VERB`var. `PARAMS`Var будет содержать все маркеры в виде именованных ключей, а также все маркеры и подстановочные знаки в виде цифровых ключей, в зависимости от их порядка появления.

Генерируйте ошибку 404, когда маркированный класс не существует.

**Обратите внимание:** если статический и динамический шаблон маршрута совпадают с текущим URI, то _статический_ шаблон маршрута имеет приоритет.

#### вызов <a id="call"></a>

**Выполнить обратный вызов/крючки \(поддерживает формат 'class - &gt;method' \)**

```text
mixed|false call ( callback $func [, mixed $args = NULL [, string $hooks = '' ]] )
```

Этот метод предоставляет возможность вызывать обратные вызовы и их аргументы. F3 распознает их как допустимые обратные вызовы:

* Анонимные / лямбда-функции \(они же замыкания\)
* `array('class','method')`
* `class::method` статический метод
* `class->method` эквивалентный `array(new class,method)`

`$args` if specified предоставляет средство выполнения обратного вызова с параметрами.

`$hooks` используется `route()`методом для указания функций до и после выполнения`beforeroute()`, т. е.`afterroute()` \(см. раздел [обработчики событий](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/routing-engine#event-handlers) для получения дополнительных объяснений `beforeroute()`и`afterroute()`\)

#### цепь <a id="chain"></a>

**Выполните указанные обратные вызовы последовательно; примените одни и те же аргументы ко всем обратным вызовам**

```text
array chain ( array|string $funcs [, mixed $args = NULL ] )
```

Этот метод вызывает несколько обратных вызовов подряд:

```text
echo $f3->chain('a; b; c', 0);

function a($n) {
	return 'a: '.($n+1); // a: 1
}

function b($n) {
	return 'b: '.($n+1); // b: 1
}

function c($n) {
	return 'c: '.($n+1); // c: 1
}
```

#### реле <a id="relay"></a>

**Выполнить указанные обратные вызовы последовательно; ретранслировать результат предыдущего обратного вызова в качестве аргумента следующему обратному вызову**

```text
array relay ( array|string $funcs [, mixed $args = NULL ] )
```

Этот метод вызывает обратный вызов последовательно, как [цепочка](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#chain), но применяет результат первой функции в качестве аргумента последующей функции, т. е.:

```text
echo $f3->relay('a; b; c', 0);

function a($n) {
	return 'a: '.($n+1); // a: 1
}

function b($n) {
	return 'b: '.($n+1); // b: 2
}

function c($n) {
	return 'c: '.($n+1); // c: 3
}
```

### файловая система <a id="file-system"></a>

#### Копировать <a id="copy"></a>

**Нативная функция PHP**

```text
bool copy ( string $source , string $dest [, resource $context ] )
```

Чтобы скопировать файл, используйте собственную [`copy()`](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/php.net/manual/en/function.copy.php) функцию PHP. Показано здесь для удобства.

#### удалить \(unlink\) <a id="delete-unlink"></a>

**Нативная функция PHP**

```text
bool unlink ( string $filename [, resource $context ] )
```

Чтобы удалить файл, используйте собственную [`unlink()`](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/php.net/manual/en/function.unlink.php) функцию PHP. Показано здесь для удобства.

#### переместить \(переименовать с помощью diff path\) <a id="move-rename-using-diff-path"></a>

**Нативная функция PHP**

```text
bool rename ( string $oldname , string $newname [, resource $context ] )
```

Чтобы переместить файл, используйте собственную [`rename()`](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/php.net/manual/en/function.rename.php) функцию PHP, меняющую путь во втором аргументе. Показано здесь для удобства.

#### взаимное исключение <a id="mutex"></a>

**Создайте мьютекс, вызовите обратный вызов, а затем отбросьте владение, когда закончите**

```text
mixed mutex ( string $id, callback $func [, mixed $args = NULL ] )
```

[Взаимное исключение \(мьютекс\)](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/en.wikipedia.org/wiki/Mutual_exclusion)-это кросс-платформенный механизм синхронизации доступа к ресурсам таким образом, что процесс, получивший мьютекс, получает эксклюзивный доступ к ресурсу. Другие процессы, пытающиеся получить тот же мьютекс, будут находиться в приостановленном состоянии до тех пор, пока мьютекс не будет освобожден.

```text
$f3->mutex('test',function() {
	// Critical section
	session_start();
	$contents=file_get_contents('mutex');
	sleep(5);
	file_put_contents('mutex',$contents.date('r').' '.session_id()."\n");
});
```

Аргументы могут быть переданы в обратный вызов:

```text
$f3->mutex('my-mutex-id',function($path,$str) {
	@file_put_contents($path,$str);
},array('/foo/bar/facts.txt','F3 is cool'));
```

#### Читать <a id="read"></a>

**Read file \(с возможностью применения Unix LF в качестве стандартного окончания строки\)**

```text
string|FALSE read ( string $file [, bool $lf = FALSE ] )
```

Использует функцию [`file_get_contents()`](https://z5h64q92x9.net/proxy_u/en-ru.ru/www.php.net/file_get_contents) PHP для чтения всего `$file`текста и возврата его в виде строки. Возврат `FALSE`после неудачи.

Если `$lf`есть`TRUE`, то преобразование EOL в формат UNIX LF выполняется для всех оканчивающихся строк.

#### выпуск <a id="rel"></a>

**Обратный путь относительно базового каталога**

```text
string rel ( string $url )
```

Пример:

```text
$f3->set('BASE','http://fatfreeframework.com/');
echo $f3->rel( 'http://fatfreeframework.com/gui/img/supported_dbs.jpg' ); // 'gui/img/supported_dbs.jpg'
```

#### переименовать <a id="rename"></a>

**Нативная функция PHP**

```text
bool rename ( string $oldname , string $newname [, resource $context ] )
```

Чтобы переименовать файл, используйте встроенную [`rename()`](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/php.net/manual/en/function.rename.php) функцию PHP. Показано здесь для удобства.

#### писать <a id="write"></a>

**Эксклюзивная запись файла**

```text
int|FALSE write ( string $file, mixed $data [, bool $append = FALSE ] )
```

Использует функцию [`file_put_contents()`](https://z5h64q92x9.net/proxy_u/en-ru.ru/www.php.net/file_put_contents) PHP с флагом `LOCK_EX` PHP для получения эксклюзивной блокировки файла при переходе к записи.

Если `$append`есть `TRUE`и `$file`уже завершается, `$data`то он добавляется к содержимому файла вместо того, чтобы перезаписывать его.

### Разное <a id="misc"></a>

#### пример <a id="instance"></a>

**Возвращаемый экземпляр класса**

```text
Base $f3 = \Base::instance();
```

Это используется для захвата экземпляра фреймворка в любой точке кода.

#### выкинуть <a id="abort"></a>

**Отключить HTTP-клиент**

```text
void abort ( )
```

Этот метод может быть удобен для прерывания соединения с клиентом и отправки ему текущего ответа, но на самом деле продолжить выполнение скрипта после этого момента для выполнения трудоемких задач, таких как отправка SMTP-почты или генерация больших PDF-файлов без каких-либо задержек для клиента.

#### блокированный <a id="blacklisted"></a>

**Поиск IP-адреса посетителя по общим службам черного списка DNS**

```text
bool blacklisted ( string $ip )
```

Эта функция вызывается во время начальной загрузки приложения и будет искать IP-адрес посетителей по общим службам черного списка DNS, определенным [системной переменной DNSBL](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/quick-reference#dnsbl). Это очень полезно для защиты вашего приложения от спам-ботов или DOS-атак.

**Return `TRUE`если IPv4 `$ip`адрес присутствует в** [**DNSBL**](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/quick-reference#dnsbl)

#### компилировать <a id="compile"></a>

**Преобразование токена JS-стиля в PHP-выражение**

```text
string compile ( string $str )
```

Этот метод в основном используется [классом предварительного просмотра](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/preview), облегченным шаблонизатором шаблонов, для преобразования токенов в переменные.

Пример:

```text
$f3->compile('@RAINBOW.cyan'); // returns: $RAINBOW['cyan']
```

#### конфиг <a id="config"></a>

**Настройте фреймворк в соответствии с .настройки файла в стиле ini**

```text
null config ( string $file [, bool $allow = FALSE ])
```

Это позволит проанализировать конфигурационный файл, предоставленный фреймворком, `$file`и настроить его с помощью переменных и маршрутов. Если указан 2-й аргумент`$allow`, строки шаблона интерпретируются, что делает возможными динамические конфигурации:

```text
[globals]
foo = bar
baz = {{@foo}}
```

Смотрите раздел руководства пользователя о [файлах конфигурации](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/framework-variables#configuration-files), чтобы получить полное описание того, как настроить ваш ini-файл.

#### константы <a id="constants"></a>

**Преобразование констант класса в массив**

```text
array constants ( object | string $class [, string $prefix = '' ] )
```

Пример, при условии, что у нас есть `Foo\Bar`класс, определенный следующим образом:

```text
namespace Foo;
class Bar {
  const STATUS_Inactive=0;
  const STATUS_Active=1;
  const E_NotFound='Not found';
}
```

вы можете получить доступ ко всем константам с префиксом`STATUS_`:

```text
$constants=$f3->constants('Foo\Bar','STATUS_');
var_dump($constants);
// outputs:
array(2) {
  ["Inactive"]=>int(0)
  ["Active"]=>int(1)
}
```

NB: передача объекта также работает: `$f3->constants($bar,'STATUS_')`.

#### свалка <a id="dump"></a>

**Выражение Dump \(\_echo\_\) с подсветкой синтаксиса**

```text
null dump ( mixed $expr )
```

_Обратите внимание: подсветка синтаксиса зависит от уровня_ [_отладки_](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/quick-reference#debug).

#### ошибка <a id="error"></a>

**Выполнить обработчик ошибок**

```text
null error ( int $code [, string $text = '' [, array $trace = NULL [, int $level = 0 ]]] )
```

Вызов этой функции регистрирует ошибку и выполняет обработчик ONERROR, если он определен. В противном случае он будет отображать страницу ошибок по умолчанию в HTML для синхронных запросов или выдавать строку JSON для AJAX-запросов.

#### истекать <a id="expire"></a>

**Отправка метаданных кэша на HTTP-клиент**

```text
void expire ( [ int $secs = 0 ] )
```

Нет необходимости вызывать этот метод напрямую, поскольку он автоматически вызывается фреймворком во время выполнения, в зависимости от того, следует ли кэшировать страницу или нет. Фреймворк отправляет необходимые заголовки управления http-кэшем в браузер, так что вам не нужно отправлять их самостоятельно.

```text
$f3->expire(0); // sends 'Cache-Control: no-cache, no-store, must-revalidate'
```

#### выделить <a id="highlight"></a>

**Применить подсветку синтаксиса**

```text
string highlight ( string $text )
```

Применяет подсветку синтаксиса к заданной строке и возвращает выделенную строку.

Пример:

```text
$highlighted_code = $f3->highlight( '$fatfree->rocks(\'FAST\' AND $light)' );
```

ВОЗВРАТ:

```text
<code><span class="variable">$fatfree</span><span class="object_operator">-&gt;</span><span class="string">rocks</span><span>(</span><span class="constant_encapsed_string">'FAST'</span><span class="whitespace"> </span><span class="logical_and">AND</span><span class="whitespace"> </span><span class="variable">$light</span><span>)</span></code>
```

Имейте в виду, что вам нужен "код".таблица стилей css позволяет правильно видеть подсветку синтаксиса на страницах Вашего браузера. Вы можете включить его в свои страницы с помощьюcss "rel=" таблица стилей " / &gt; \(код.css упаковывается в папку framework ' lib/' \)

#### Статус <a id="status"></a>

**Отправить заголовок состояния HTTP / 1.1; вернуть текстовый эквивалент кода состояния**

```text
string status ( int $code )
```

Используйте этот метод для отправки клиенту различных [сообщений о состоянии HTTP](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/www.w3.org/Protocols/rfc2616/rfc2616-sec10.html), например

```text
$f3->status(404); // Sends a '404 Not Found' client error
$f3->status(407); // Sends a '407 Proxy Authentication Required' client error
$f3->status(503); // Sends a '503 Service Unavailable' server error
```

#### разгружать <a id="unload"></a>

**Выполнение последовательности завершения работы фреймворка/приложения**

```text
null unload ( string $cwd )
```

Во-первых , изменяет текущий каталог PHP на каталог`$cwd`, записывает данные сеанса и завершает сеанс вызовом функции `session_commit()` PHP.

Затем он завершает работу приложения и вызывает обработчик завершения работы, определенный в [UNLOAD](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/quick-reference#unload).

В качестве окончательного запасного варианта возникает ошибка HTTP, `500`если одно из следующих `E_ERROR, E_PARSE, E_CORE_ERROR, E_COMPILE_ERROR`действий обнаружено и не обработано обработчиком завершения работы.

#### рекурсивный <a id="recursive"></a>

**Рекурсивно вызывать обратный вызов для всех типов данных**

```text
mixed recursive( mixed $arg , callable $func [, array $stack = NULL ] )
```

Рекурсивный метод берет ваш смешанный `$arg`и применяет `$func`обратный вызов к любому значению глубокого вложенного массива или свойству объекта, которое может быть найдено, и возвращает копию `$arg`. Вот небольшой пример:

```text
// ensure proper UTF-8 sequences before encoding
echo json_encode($f3->recursive($out,function($val){
	return mb_convert_encoding($val,'UTF-8','UTF-8');
}));
```

**Обратите внимание:** для вызова обратного вызова \`$func\` на объектах \`PHP &gt;= 5.4\` требуется. В противном случае объекты возвращаются без дальнейшей обработки. Пожалуйста, имейте в виду, что возвращаемые объекты являются клонами, и обратный вызов применяется только к доступным нестатическим свойствам.

#### до тех пор <a id="until"></a>

**Цикл до тех пор, пока обратный вызов не вернет TRUE \(для длительного опроса\)**

```text
mixed until ( callable $func [, array $args = null [, int $timeout = 60 ] ] )
```

[Обратный](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#call) `$func` вызов вызывается один раз в секунду с аргументом `$args`до тех пор, пока одно из следующих условий не завершит цикл:

* HTTP-клиент отключается
* Тайм-аут через `$timeout`несколько секунд
* Тайм-аут за одну секунду до достижения настроенного PHP `max_execution_time`
* Повторное открытие сеанса завершается неудачей
* Обратный `$func`вызов возвращает значение boolean casted равное `true`

F3 закрывает сеанс после и снова открывает сеанс перед вызовом обратного `$func`вызова, чтобы предотвратить блокировку сеанса, которая в противном случае блокировала бы одновременные запросы с одним и тем же сеансом.**Обратите внимание:** \`$timeout\` ограничен конфигурацией PHP \`max\_execution\_time\`.

