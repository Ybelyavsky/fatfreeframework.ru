# Переменные



### основное использование <a id="basic-use"></a>

Переменные, определенные в Fat-Free, являются глобальными, то есть они могут быть доступны любому компоненту. Глобалы фреймворка не идентичны глобалам PHP. Переменная F3 с именем `content`не идентична PHP `$content`. F3 сам по себе является доменным языком и поддерживает свою собственную отдельную таблицу символов для системных и прикладных переменных. Фреймворк, как и любая хорошо разработанная объектно-ориентированная программа, не загрязняет глобальное пространство имен PHP константами, переменными, функциями или классами, которые могут конфликтовать с любым приложением. В отличие от других фреймворков, F3 не использует PHP `define()` заявление. Все константы фреймворка ограничены классами.

Чтобы присвоить значение F3 переменной:

```text
$f3->set('var',value)
```

**Примечание:** F3 переменные принимают все типы данных PHP, включая объекты и анонимные функции.

Чтобы задать сразу несколько переменных:

```text
$f3->mset(
    array(
        'foo'=>'bar',
        'baz'=>123
    )
);
```

Чтобы получить значение переменной фреймворка с именем`var`:

```text
$f3->get('var')
```

Чтобы удалить F3 переменную из памяти, если она вам больше не нужна \(отбросьте ее, чтобы она не мешала другим вашим функциям / методам\), используйте этот метод:

```text
$f3->clear('var')
```

Чтобы узнать, была ли переменная определена ранее:

```text
$f3->exists('var')
```

### Глобальные <a id="globals"></a>

F3 поддерживает свою собственную таблицу символов для переменных фреймворка и приложения, которые не зависят от PHP. некоторые переменные сопоставляются с глобалами PHP. В F3 `SESSION`- это эквивалент`$_SESSION`, а `REQUEST`карты`$_REQUEST`-нет . Рекомендуется использовать переменные фреймворка, а не PHP, чтобы помочь вам с передачей данных между различными функциями, классами и методами. У них есть и другие преимущества:

* Переменные фреймворка можно использовать непосредственно в шаблонах.
* Вам не нужно указывать PHP ссылаться на переменную вне текущей области видимости, используя глобальное ключевое слово внутри каждой функции или метода. Все переменные F3 являются глобальными для вашего приложения.
* Установка F3 эквивалента PHP `SESSION`также изменяет базовую версию PHP `$_SESSION`. Изменение последнего также изменяет аналог фреймворка.

Fat-Free не поддерживает просто тупое хранилище переменных и их значений. Он также может автоматизировать управление сеансами и другие вещи. Назначение или извлечение значения через `SESSION`переменную F3 автоматически запускает сеанс. Если вы используете `$_SESSION`\(или связанные с сеансом функции\) непосредственно, а не переменную фреймворка`SESSION`, ваше приложение становится ответственным за управление сеансами.

As a rule, framework variables do not persist between HTTP requests. Only `SESSION` and `COOKIE` \(and their elements\) which are mapped to PHP's `$_SESSION` and `$_COOKIE` global variables are exempt from the stateless nature of HTTP.

There are several predefined global variables used internally by Fat-Free, and you can certainly utilize them in your application. Be sure you know what you're doing. Altering some Fat-Free global variables may result in unexpected framework behavior.

The framework has several variables to help you keep your files and directory structures organized. We've seen how we can automate class loading by using the `AUTOLOAD`. There's a `UI` global variable, which contains the path pointing to the location of your HTML views/templates. `DEBUG` is another variable you'll be using quite often during application development and it's used for setting the verbosity of error traces.

Refer to the [Quick Reference](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/quick-reference) for a comprehensive list of built-in framework variables.

### Naming Rules <a id="naming-rules"></a>

A framework variable may contain any number of letters, digits and underscores. It must start with an alpha character and should have no spaces. Variable names are case-sensitive.

F3 uses all-caps for internal predefined global variables. Nothing stops you from using variable names consisting of all-caps in your own program, but as a general rule, stick to lowercase \(or camelCase\) when you set up your own variables so you can avoid any possible conflict with current and future framework releases.

Вы не должны использовать зарезервированные PHP слова, такие как`if`, `for`, `class`, и т.д.`default` как имена переменных фреймворка. Это может привести к непредсказуемым результатам.

### Работа со строковыми и массивными переменными <a id="working-with-string-and-array-variables"></a>

F3 также предоставляет ряд инструментов, которые помогут вам работать с переменными фреймворка.

```text
$f3->set('a','fire');
$f3->concat('a','cracker');
echo $f3->get('a'); // returns the string 'firecracker'

$f3->copy('a','b');
echo $f3->get('b'); // returns the same string: 'firecracker'
```

F3 также предоставляет некоторые примитивные методы для работы с переменными массива:

```text
$f3->set('colors',array('red','blue','yellow'));
$f3->push('colors','green'); // works like PHP's array_push()
echo $f3->pop('colors'); // returns 'green'

$f3->unshift('colors','purple'); // similar to array_unshift()
echo $f3->shift('colors'); // returns 'purple'

$f3->set('grays',array('light','dark'));
$result=$f3->merge('colors','grays'); // merges the two arrays
```

### Структуры Каталогов Do-It-Yourself <a id="do-it-yourself-directory-structures"></a>

В отличие от других фреймворков, которые имеют жесткую структуру папок, F3 дает вам большую гибкость. У вас может быть структура папок, которая выглядит следующим образом \(заключенные в скобки слова во всех заглавных буквах представляют переменные фреймворка F3, которые нуждаются в настройке\):

* / ваш веб-корень, где индекс.php находится здесь
  * файлы приложений/ приложений
    * dict / LOCALES, опционально
    * контроллеры/
    * журналы/ журналы, опционально
    * Модели/
    * представления / пользовательский интерфейс
  * CSS/
  * JS/
  * lib/ вы можете хранить базу данных.php здесь
  * tmp / TEMP
    * кэш/ CACHE

Не стесняйтесь организовывать свои файлы и каталоги так, как вы хотите. Просто установите соответствующие глобальные переменные F3. Если вы хотите действительно безопасный сайт, Fat-Free даже позволяет хранить все ваши файлы в недоступном для Интернета каталоге. Единственное требование состоит в том , чтобы вы оставили `index.php.htaccess`и свои публичные файлы, такие как CSS, JavaScript, изображения и т. д. в пути, видимом вашему браузеру.

### Об обработчике ошибок F3 <a id="about-the-f3-error-handler"></a>

Fat-Free генерирует свои собственные HTML-страницы ошибок с трассировками стека,чтобы помочь вам в отладке. Вот вам пример:

> #### внутренняя ошибка сервера
>
> strpos \(\) ожидает по крайней мере 2 параметра, 0 задано
>
> ```text
> • var/html/dev/main.php:96 strpos()
> • var/html/dev/index.php:16 Base->run()
> ```

Если вы чувствуете, что это слишком просто или хотите сделать что-то другое, когда возникает ошибка, вы можете создать свой собственный обработчик ошибок:

```text
$f3->set('ONERROR',
    function($f3) {
        // custom error handler code goes here
        // use this if you want to display errors in a
        // format consistent with your site's theme
        echo $f3->get('ERROR.text');
    }
);
```

F3 поддерживает глобальную переменную, содержащую сведения о последней ошибке, возникшей в вашем приложении. `ERROR`Переменная представляет собой массив, структурированный следующим образом:

```text
`ERROR.code` - the HTTP status error code (`404`, `500`, etc.)
`ERROR.status` - a brief description of the HTTP status code. e.g. `'Not Found'`
`ERROR.text` - error context
`ERROR.trace` - stack trace stored in an `array()`
`ERROR.level` - error reporting level (`E_WARNING`, `E_STRICT`, etc.)
```

Если ваш проект использует шаблоны, вы можете обработать потенциальные ошибки шаблонов, очистив любой существующий выходной буфер и отобразив вместо него новую страницу:

```text
$f3->set('ONERROR',
    function($f3) {
	// recursively clear existing output buffers:
        while (ob_get_level())
            ob_end_clean();
        // your fresh page here:
        echo $f3->get('ERROR.text');
    }
);
```

При разработке приложения лучше всего установить максимальный уровень отладки, чтобы можно было проследить все ошибки до их первопричины:

```text
$f3->set('DEBUG',3);
```

Просто вставьте команду в последовательность начальной загрузки вашего приложения.

Как только ваше приложение будет готово к выпуску, просто удалите инструкцию из вашего приложения или замените ее на:

```text
$f3->set('DEBUG',0);
```

Это приведет к подавлению вывода трассировки стека на любой сгенерированной системой HTML-странице ошибок \(потому что она не предназначена для просмотра посетителями вашего сайта\).

`DEBUG` может иметь значения в диапазоне от `0`\(stack trace suppressed\) до `3`\(most verbose with class and function call logs\).

**Не забывай!** Трассировки стека могут содержать пути, имена файлов, команды базы данных, имена пользователей и пароли. Вы можете подвергнуть свой веб-сайт ненужным рискам безопасности, если не установите `DEBUG`глобальную переменную `0`в рабочей среде.

Можно настроить описание ошибки в момент запуска ошибки, передав новое описание в качестве второго аргумента, например :

```text
$f3->error(401, "The information necessary to grant access is missing from the request.");
```

### Configuration Files <a id="configuration-files"></a>

If your application needs to be user-configurable, F3 provides a handy method for reading configuration files to set up your application. This way, you and your users can tweak the application without altering any PHP code.

There are 4 predefined section names:

* `[globals]` for global variables definitions
* `[routes]` for routes definitions
* `[maps]` for route maps definitions
* `[redirects]` for redirecting routes

**NB**: `[globals]` is assumed if no section has been provided. You can combine all sections in a single configuration file - although having `[routes]`, `[maps]`, and `[redirects]` in a separate file is recommended. This way you can allow end-users to modify some application-specific flags, and at the same time restrict them from meddling with your routing logic.

Here's how to use the different sections:.

#### \[globals\] <a id="globals"></a>

Вместо того чтобы создавать PHP скрипт содержащий следующий пример кода:

```text
$f3->set('num',123);
$f3->set('str','abc');
$f3->set('hash',array('x'=>1,'y'=>2,'z'=>3));
$f3->set('items',array(7,8,9));
$f3->set('mix',array('this',123.45,FALSE));
```

Вы можете создать конфигурационный файл, который делает то же самое:

```text
[globals]
num=123
; this is a regular string
str=abc
; another way of assigning strings
str="abc"
; this is an array
hash[x]=1
hash[y]=2
hash[z]=3
; dot-notation is recognized too
hash.x=1
hash.y=2
hash.z=3
; this is also an array
items=7,8,9
; array with mixed elements
mix="this",123.45,FALSE
```

Вместо длинных `$f3->set()`инструкций в коде вы можете поручить фреймворку загрузить конфигурационный файл в качестве замены кода. Давайте сохраним приведенный выше текст в качестве настройки.контекстно-свободная грамматика. Тогда мы можем назвать его простым:

```text
$f3->config('setup.cfg');
```

Строковые значения не должны заключаться в кавычки, если только вы не хотите включить начальные или конечные пробелы. Если запятая должна рассматриваться как часть строки, заключите ее в двойные кавычки - в противном случае значение будет рассматриваться как массив \(запятая используется в качестве разделителя элементов массива\). Строки могут охватывать несколько строк:

```text
[globals]
str="this is a \
very long \
string"
```

#### \[пути сообщения\] <a id="routes"></a>

F3 также дает вам возможность определять маршруты HTTP в конфигурационных файлах:

```text
[routes]
GET /=home
GET /404=App->page404
GET /page/@num=Page->controller
; Cache the route for 10 minutes
GET /contact=App->contact, 600
; named route
GET @about: /about=Page->about
```

#### \[карты\] <a id="maps"></a>

Карты маршрутов также можно определить в конфигурационных файлах:

```text
[maps]
/blog=Blog\Login
/blog/@controller=Blog\@controller
```

#### \[перенаправляет\] <a id="redirects"></a>

Вы также можете перенаправлять устаревшие маршруты на новые страницы в конфигурационных файлах:

```text
[redirects]
GET|HEAD /obsoletepage = /newpage
GET|HEAD /dash = @dashboard
GET|HEAD /search = https://www.google.com
```

#### \[конфигурации\] <a id="configs"></a>

Вы также можете включить другие конфигурационные файлы.

```text
[configs]
app/routes.ini = true
app/app_ext.ini = false
```

Не позволяйте правильному значению сбить вас с толку, оба файла выше включены. Параметр `true`/`false` boolean запускает `$allow`аргумент метода [config](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#config), который позволяет разрешить динамический токен в ваших конфигурационных файлах.

#### Пользовательский раздел <a id="custom-sections"></a>

Любое другое название раздела, кроме 3 выше, интерпретируется как `[globals]`раздел с префиксом имени раздела.

Итак следующее:

```text
[foo]
a = 1
b = 2

[foo.hash]
x = 1
y = 2
z = 3
```

эквивалентно:

```text
$f3->set('foo',array(
  'a' => 1,
  'b' => 2,
  'hash' => array(
    'x' => 1,
    'y' => 2,
    'z' => 3
  )
));
```

#### Секционные Крючки <a id="section-hooks"></a>

Кроме того, можно определить пользовательский обратный вызов, который применяется к каждому значению:

```text
[foo.bar:strtoupper]
x = hello
y = world
```

