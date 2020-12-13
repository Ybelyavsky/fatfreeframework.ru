# Плагины

## Плагины <a id="plug-ins"></a>

### О Плагинах F3 <a id="about-f3-plug-ins"></a>

Плагины - это не что иное, как автоматически загружаемые классы, которые используют встроенные фреймворки для расширения возможностей и функциональности F3. Если вы хотите внести свой вклад, оставьте заметку в [обезжиренной дискуссионной зоне](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/groups.google.com/forum/#!forum/f3-framework), размещенной Google Groups, или расскажите нам об этом в `#fatfree`IRC-канале FreeNode. Кто-то еще может быть вовлечен в подобный проект. Рамочное сообщество будет очень благодарно, если мы объединим наши усилия.

### Изображение <a id="image"></a>

#### Капча изображения <a id="captcha-images"></a>

Могут быть случаи, когда вы хотите сделать свои формы более защищенными от спам-ботов и вредоносных автоматических скриптов. F3 предоставляет `captcha()`метод для генерации изображений со случайным текстом, которые предназначены для распознавания только людьми.

```text
$img = new Image();
$img->captcha('fonts/CoolFont.ttf',16,5,'SESSION.captcha_code');
$img->render();
```

Этот пример генерирует случайное изображение на основе нужного вам шрифта TrueType. `fonts/`Папка - это подпапка в `UI`пути приложения. Второй параметр указывает размер шрифта \(выполняется процесс увеличения в 2 раза, то есть размер 16 будет производить изображение высотой около 32 пикселей\). Третий параметр определяет количество шестнадцатеричных символов для генерации, допустимые значения между минимумом 4 и максимумом 13.

Последний параметр представляет собой имя переменной F3. Используйте его для хранения строкового эквивалента изображения CAPTCHA,чтобы сравнить его с пользовательским вводом. Чтобы сделать перезагрузку строки безопасной, мы задали переменную сеанса: `SESSION.captcha_code`which maps to`$_SESSION['captcha_code']`, которую вы можете использовать позже, чтобы проверить, соответствует ли входной элемент в отправленной форме этой строке.

#### обработка изображений <a id="image-processing"></a>

Плагин image также предоставляет дополнительные функции обработки для масштабирования, обрезки и наложения изображений, а также регулировки яркости, контрастности и многого другого. Пожалуйста, посмотрите на [ссылку API класса изображений](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/image) для описания дополнительных функций.

### Лог <a id="log"></a>

Посмотрите, как легко создать пользовательский регистратор, чтобы сохранить все Ваши интересные события приложения.

```text
$logger = new \Log('app-events.log');
$logger->write('User John logged in.');
```

### Снижение <a id="markdown"></a>

Конвертируйте ваш любимый [текст Markdown \(Wikipedia\)](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/en.wikipedia.org/wiki/Markdown) в HTML.

```text
$filePath = 'content/readme.md';
$fileContent = $f3->read($filePath); // read file contents

echo \Markdown::instance()->convert($fileContent);
```

### Сеть <a id="web"></a>

#### Захват данных с другого сайта <a id="grabbing-data-from-another-site"></a>

Мы рассмотрели почти все функции, доступные в фреймворке для запуска автономного веб-сервера. Для большинства приложений эти функции будут служить вам довольно хорошо. Но что делать, если вашему приложению нужны данные с другого веб-сервера в сети? У F3 есть веб-плагин, который поможет вам в этой ситуации:

```text
$web=new Web;
$request=$web->request('http://www.google.com/');
// another way to do it:
$request=Web::instance()->request('http://www.google.com/');
```

Этот простой пример отправляет HTTP-запрос на страницу, расположенную по адресу www.google.com и хранит его в переменной `$request`PHP. `request()`Метод возвращает массив, содержащий HTTP-ответ таким образом, что `$request['headers']`и `$request['body']`представляют собой заголовки ответа и тело соответственно. Мы могли бы сохранить содержимое с помощью команды F3:: set или отправить Эхо-вывод непосредственно в браузер. Извлечение другой HTML-страницы из сети может не иметь никакой практической цели. Но это может быть особенно полезно в спокойных приложениях, таких как запрос сервера CouchDB.

```text
$host='localhost:5984';
$web->request($host.'/_all_dbs'),
$web->request($host.'/testdb/',array('method'=>'PUT'));
```

Возможно, вы заметили, что вы можете передать массив дополнительных опций этому `request()`методу:

```text
$web->request(
    'https://www.example.com:443?'.
    http_build_query(
        array(
            'key1'=>'value1',
            'key2'=>'value2'
        )
    ),
    array(
        'header'=>array(
            'Accept: text/html,application/xhtml+xml,application/xml',
            'Accept-Language: en-us'
        ),
        'follow_location'=>FALSE,
        'max_redirects'=>30,
        'ignore_errors'=>TRUE
    )
);
```

Если переменная фреймворка `CACHE`включена и удаленный сервер дает вашему приложению команду кэшировать ответ на HTTP-запрос, F3 будет выполнять этот запрос и извлекать кэшированный ответ каждый раз, когда фреймворк получает аналогичный запрос от вашего приложения, таким образом ведя себя как браузер.

Fat-Free будет использовать любые средства, доступные на вашем веб-сервере для запуска `request()`метода: PHP stream wrappers \(`allow_url_fopen`\), модуль cURL или низкоуровневые сокеты.

#### Обработка Загрузки Файлов <a id="handling-file-downloads"></a>

В F3 есть утилита для отправки файлов на HTTP-клиент, то есть для выполнения запросов на загрузку. Вы можете использовать его, чтобы скрыть реальный путь к вашим файлам загрузки. Это добавляет некоторый уровень безопасности, потому что пользователи не смогут загружать файлы, если они не знают имена файлов и их расположение. Вот как это делается:

```text
$f3->route('GET /downloads/@filename',
    function($f3,$args) {
        // send() method returns FALSE if file doesn't exist
        if (!Web::instance()->send('/real/path/'.$args['filename']))
            // Generate an HTTP 404
        $f3->error(404);
    }
);
```

#### Удаленное управление и распределенные приложения <a id="remoting-and-distributed-applications"></a>

Этот `request()`метод также может быть использован в сложных SOAP-или XML-RPC-приложениях, если вы обнаружите необходимость в другом веб-сервере для обработки данных от имени Вашего компьютера - таким образом, используя возможности распределенных вычислений. W3Schools.com имеет отличный учебник по мылу. С другой стороны, TutorialsPoint.com дает хороший обзор XML-RPC.

### больше плагинов <a id="more-plug-ins"></a>

Есть еще много плагинов, доступных для F3. Взгляните на их ссылку API, чтобы получить больше информации о них.

* [Аудит](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/audit)
* [Автор](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/auth)
* [Корзина](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/basket)
* [Изображение](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/image)
* [Лог](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/log)
* [Снижение](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/markdown)
* [протокол SMTP](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/smtp)
* [Шаблон](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/template)
* [Тест](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/test)
* [Сеть](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/web)
* [Гео](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/geo)
* [OpenID](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/openid)
* [Пингбэк](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/pingback)
* [Google Static Maps API v2](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/google-static-maps)

Все еще не нашли то, что искали? Возможно, вы найдете его в [разделе плагинов, внесенных пользователем](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/development#user-plugins).

