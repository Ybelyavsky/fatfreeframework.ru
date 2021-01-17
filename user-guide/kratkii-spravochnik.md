# Краткий справочник

### системная переменная <a id="system-variables"></a>

Чтобы использовать эти переменные, просто получите их из объекта $f3, как в:

```text
$current_url = $f3->get('PATH');
```

#### АГЕНТ <a id="agent"></a>

**Тип:** `string`, `Read-only`

Строка, содержащая автоматически обнаруженный агент пользователя HTTP, например `'Mozilla/5.0 (Linux; Android 4.2.2; Nexus 7) AppleWebKit/537.31'`

#### АЯКС <a id="ajax"></a>

**Тип:** `bool`, `Read-only`

`TRUE` если обнаружен XML-HTTP-запрос, `FALSE`в противном случае. Значение по умолчанию: результат выражения `$headers['X-Requested-With']=='XMLHttpRequest'`

#### ПСЕВДОНИМ <a id="alias"></a>

**Тип:** `string`

Содержит псевдоним \(имя\) текущего маршрута. Пусто, если текущий маршрут не назван.

#### ПСЕВДОНИМЫ <a id="aliases"></a>

**Тип:** `array`

Этот массив содержит все [именованные маршруты](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/routing-engine#named-routes), которые могут быть использованы для отображения соответствующих URL-адресов ссылок в ваших шаблонах.

#### АВТОЗАГРУЗКА <a id="autoload"></a>

**Тип:** `string|array`     **По Умолчанию:** `'./'`

Путь поиска \(\*\*s\*\*\) для пользовательских классов PHP, которые фреймворк попытается загрузить автоматически во время выполнения. При указании нескольких путей, в качестве разделителя путей можно использовать трубу \(`|`\), запятую \(`,`\) или точку с запятой \(`;`\).**Помните:** пути должны заканчиваться косой чертой. Например: `$f3->set('AUTOLOAD', 'app/;inc/,./');`

[Смотрите здесь](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/routing-engine#the-f3-autoloader) для получения более подробной информации.**Будьте осторожны:** если вы определяете эту переменную в конфигурационном файле, помните, что запятые имеют особое значение \(разделитель массива\).  
Таким образом, следующие синтаксисы являются допустимыми:

* `AUTOLOAD = foo/;bar/,Customer::casehandler`
* `AUTOLOAD = foo/;bar/`
* `AUTOLOAD = foo/|bar/`
* `AUTOLOAD = "foo/,bar/"`

пока `AUTOLOAD = foo/,bar/`нет.

#### БАЗА <a id="base"></a>

**Тип:** `string`, `Read-only`     **по умолчанию:** автоопределение

Путь к `index.php`главному / переднему контроллеру.

#### ТЕЛО <a id="body"></a>

**Тип:** `string`, `Read-only`

Тело HTTP-запроса для спокойной постобработки. Содержит `php://input`поток, используемый запросами PUT, если [RAW](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/quick-reference#raw) есть `false`.

#### КЭШ <a id="cache"></a>

**Тип:** `bool|string`     **По Умолчанию:** `FALSE`

Кэш-сервер. F3 может обрабатывать Memcache module, APC, WinCache, XCache и кэш на основе файловой системы.

Например: если вы хотите использовать модуль memcache, требуется строка конфигурации, например `$f3->set('CACHE','memcache=localhost')`\(порт 11211 по умолчанию\) или `$f3->set('CACHE','memcache=192.168.72.72:11212')`.

Когда установлено значение`TRUE`, или когда соединение с указанным выше сервером memcached не удалось, F3 автоматически обнаружит в этом порядке наличие APC, WinCache, XCache и использует первый доступный из этих PHP-модулей. Если ни один из этих движков общей памяти не был обнаружен или доступен, в качестве резервного используется бэкэнд на основе файловой системы \(каталог по умолчанию: `tmp/cache`или вы можете указать папку вне области действия веб-сайта, например `$f3->set('CACHE','folder=/var/tmp/f3filescache/')`.

Фреймворк не использует никакого механизма кэширования при `FALSE`присвоении значения.

#### РЕГИСТРОНЕЗАВИСИМЫЙ <a id="caseless"></a>

**Тип:** `bool`     **По Умолчанию:** `TRUE`

Сопоставление шаблонов маршрутов с входящими URI по умолчанию не учитывает регистр символов. Установите `FALSE`значение, чтобы сделать его чувствительным к регистру.

#### CLI <a id="cli"></a>

**Тип:** `bool`, `Read-only`

`TRUE` если запрос исходит из интерфейса командной строки, `FALSE`если он исходит от веб-сервера.

Дополнительные сведения о том, как обрабатывать запросы CLI, см. В разделе Режим CLI.

#### КОНТЕЙНЕР <a id="container"></a>

**Тип:** `callable|Prefab|Psr\Container\ContainerInterface`

Определяет необязательный контейнер для инъекции зависимостей, используемый[`Base->call()`](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#call) и система маршрутизации. `CONTAINER` поддержка контейнеров PSR-11, [вызываемые объекты](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#call) и классы расширяются `Prefab`. `Prefab`- основанные классы должны реализовать этот `get(string $id)`метод. Вызываемые объекты получают запрошенное `$id`\(например, имя класса\) в качестве первого аргумента.

API-несовместимые сторонние контейнеры можно сделать совместимыми с крошечным адаптером.

```text
$dice = … // Configure the API-incompatible Level-2/Dice container.

$f3->set('CONTAINER', function ($class) use ($dice) {
    return $dice->create($class);
});
```

**NB:** `CONTAINER` требуется, по крайней мере, обезжиренный каркас `3.6.4`.

#### COOKIE, GET, POST, REQUEST, SESSION, FILES, SERVER, ENV <a id="cookie-get-post-request-session-files-server-env"></a>

**Тип:** `array`

Фреймворк-эквиваленты PHP globals. Для вашего удобства F3 автоматически синхронизирует эти переменные с базовыми глобалами PHP. Эти переменные могут использоваться во всем приложении. Однако прямое использование шаблонов не рекомендуется из-за рисков безопасности.

Вполне возможно, что конфигурация PHP не заполняет все глобалы. Если, например, переменные окружения отсутствуют, затем вы должны добавить `E`в директиву конфигурации PHP [`variables_order`](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/www.php.net/manual/en/ini.core.php#ini.variables-order).

#### CORS <a id="cors"></a>

**Тип:** `array`

Параметры конфигурации совместного использования ресурсов между источниками. Состоит из следующих вариантов:

* `headers`, строка или массив, по умолчанию:`''`, разрешенные заголовки в запросе
* `origin`, string или false, default:`false`, allowed origin host, i. e `*.mydomain.com`
* `credentials` bool, default:`false`, разрешить cookies
* `expose`, string or array, default:`false`, управляет тем, какие заголовки из ответа отображаются в клиентском браузере
* `ttl`, int, detault:`0`, время кэширования запроса предполетных опций

Чтобы включить базовую поддержку CORS, просто установите `CORS.origin`значение `*`. Для более определенной настройки вы можете использовать `$f3->copy('HEADERS.Origin','CORS.origin');`.

#### ОТЛАЖИВАТЬ <a id="debug"></a>

**Тип:** `integer`     **По Умолчанию:** `0`

Уровень детализации трассировки стека. Назначьте значения от 0 до 3 для повышения уровня детализации следующим образом:

* 0: подавляет журналы трассировки стека.
* 1: файлы журналов и строки.
* 2: также регистрирует классы и функции.
* 3: также регистрирует подробную информацию об объектах.

**Обратите внимание:** только значение по умолчанию `0`должно использоваться на производственных серверах.

#### ДИАКРИТИКА <a id="diacritics"></a>

**Тип:** `array`     **Default:** `array()`, пустой массив

Дополнительные пары ключ-значение для перевода символов из внешнего языка в ASCII, как это используется в [web - &gt;slug&gt;](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/web#slug).

#### DNSBL <a id="dnsbl"></a>

**Тип:** `string`     **Default:** `''`, пустая строка

Разделенный запятыми [список серверов черного списка DNS](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/whatismyipaddress.com/blacklist-check). Фреймворк генерирует `403 Forbidden`ошибку, если IPv4-адрес пользователя указан на указанном сервере\(серверах\).

#### ЭМОДЗИ <a id="emoji"></a>

**Тип:** `array`     **Default:** `array()`, пустой массив

Дополнительные пары ключ-значение токенов emoji для добавления к базовому набору, используемому при переводе строки в символы, поддерживаемые шрифтом Unicode. \(см.[`\UTF->emojify()`](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/utf-unicode-string-manager#emojify)\)

#### КОДИРОВАНИЕ <a id="encoding"></a>

**Тип:** `string`     **По Умолчанию:** `'UTF-8'`

Набор символов, используемый для [кодирования документов](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/views-and-templates#document-encoding).

#### ОШИБКА <a id="error"></a>

**Тип:** `array`, `Read-Only`

Информация о последней возникшей ошибке HTTP:

* `ERROR.code` это [код состояния HTTP](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). напр. `307`
* `ERROR.status` это краткое описание кода состояния HTTP. напр. `'Temporary Redirect'`
* `ERROR.text` содержит краткое описание ошибки.
* `ERROR.trace` используется для ошибок HTTP 500, чтобы получить трассировку стека. `string`
* `ERROR.level` - уровень отчетности об ошибках \(`E_WARNING`,`E_STRICT`, и т.д.\)

#### ПОБЕГ <a id="escape"></a>

**Тип:** `bool`     **По Умолчанию:** `TRUE`

Используется для включения/отключения автоматического экранирования [@токенов](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/quick-reference#token), используемых в шаблонах.

#### ОСВОБОЖДЕННЫЙ <a id="exempt"></a>

**Тип:** `string`     **По Умолчанию:** `NULL`

Разделенный запятыми список IPv4-адресов, освобожденных от поиска DNSBL.

#### ИСКЛЮЧЕНИЕ <a id="exception"></a>

**Тип:** `object`     **По Умолчанию:** `NULL`

Содержит объект исключения при возникновении необработанных исключений.

#### ОТСТУПЛЕНИЕ <a id="fallback"></a>

**Тип:** `string`     **По Умолчанию:** `'en'`

Язык \(и словарь\) для использования, если перевод недоступен.

#### ФОРМАТЫ <a id="formats"></a>

**Тип:** `array`

Хранение пользовательских правил форматирования для добавления поддержки нескольких форматов локализации или других случаев. Смотрите [примеры кода](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/github.com/F3Community/snippets/tree/master/formats).

```text
$f3->set('FORMATS.polish','FormatHelper->polish');
```

#### ФРАГМЕНТ <a id="fragment"></a>

**Тип:** `string`     **По Умолчанию:** `NULL`

Часть URI после необязательного фрагмента символа хэша \( \# \) `(http://www.example.org/foo.html#bar)`= 'bar'.

#### ОСТАНОВКА <a id="halt"></a>

**Тип:** `bool`     **По Умолчанию:** `TRUE`

Если `TRUE`фреймворк, зарегистрировав трассировку стека и ошибки, останавливает выполнение \(`die`без какого-либо состояния\) при _обнаружении неустранимой_ ошибки.

#### ЗАГОЛОВКИ <a id="headers"></a>

**Тип:** `array`,`Read-Only`

Заголовки HTTP-запросов, полученные сервером. например \(упрощенный\)

```text
array (
	'Host' => 'fatfreeframework.com'
	'Accept-Encoding' => 'gzip,deflate,sdch',
	'Accept-Language' => 'en-US,en;q=0.8,ja;q=0.6'
)
```

#### ВЫДЕЛИТЬ <a id="highlight"></a>

**Тип:** `bool`     **По Умолчанию:** `FALSE`

Включить / отключить подсветку синтаксиса трассировок стека и блоков кода Markdown. При включении требуется `code.css`таблица стилей.

#### ХОЗЯИН <a id="host"></a>

**Тип:** `string`,`Read-Only`

Имя хоста сервера.

#### IP <a id="ip"></a>

**Тип:** `string`,`Read-Only`

удаленный IP адрес. Фреймворк получает адрес из заголовков, если HTTP-клиент находится за прокси-сервером. Значение по умолчанию: первое совпадение `Client-IP`then `X-Forwarded-For`then`$_SERVER['REMOTE_ADDR']`, в противном случае устанавливается равным `''`

#### КУВШИН <a id="jar"></a>

**Тип:** `array`

Параметры файлов cookie по умолчанию. Состоит из следующих вариантов:

* `expire` Метка времени Unix, когда должен истечь срок действия файла cookie. По умолчанию: `0`
* `path` Путь к серверу, на котором будет доступен файл cookie. По умолчанию: `'/'`
* `domain` Домен, которому доступен файл cookie. По умолчанию: `$_SERVER['SERVER_NAME']`если доступно, else `''`
* `secure` Установите файл cookie, когда существует безопасное HTTPS-соединение. По умолчанию: `$_SERVER['HTTPS']=='on'`
* `httponly` Сделайте файл cookie доступным только по протоколу HTTP. По умолчанию: `TRUE`

Вы можете обратиться к [session\_set\_cookie\_params\(\) в руководстве по PHP](https://z5h64q92x9.net/proxy_u/en-ru.ru/www.php.net/manual/en/function.session-set-cookie-params.php) для получения дополнительной информации.

Вы также можете [посмотреть видео](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/youtu.be/a-zTNeDeqU0), которое проходит через использование файлов cookie в обезжиренном фреймворке.

#### ЯЗЫК <a id="language"></a>

**Тип:** `string`     **по умолчанию:** автоматически определяется

Текущий активный язык\(ы\). Значение используется для загрузки соответствующего файла перевода языка\(языков\) в папку, на которую указывает by `LOCALES`. По умолчанию: автоматически определяется из заголовка HTTP`Accept-Language`-запроса, например `'en-US,en,es'`.

**NB:** локаль системы загружается соответственно этой переменной. Например:

```text
$f3->set('ENCODING','UTF-8');
$f3->set('LANGUAGE','it-IT');// the locale it_IT.UTF-8 will be automatically loaded using setlocale
```

Более подробную информацию и пример смотрите в разделе локализация`Base`.

#### МЕСТО ДЕЙСТВИЯ <a id="locales"></a>

**Тип:** `string`     **По Умолчанию:** `'./'`

Расположение словарей языка\(языков\).

Чтобы включить кэширование словарей из конфигурационного файла, вам нужно написать его следующим образом:

```text
LOCALES=/path/to/lexicons | 3600
```

#### ЛОГГИРУЕМЫЙ <a id="loggable"></a>

**Тип:** `string|array`     **По Умолчанию:** `'*'`

Вы можете предоставить ему либо массив, либо разделенный запятыми/запятой список кодов состояния HTTP, чтобы разрешить передачу в `error_log()`функцию при возникновении ошибки. Это особенно полезно, когда вы создаете приложение CLI с FatFree routes и вам нужно перехватить ошибку 404 not found и отобразить пользовательское сообщение или действие.

```text
LOGGABLE='403;500;'
```

#### БРЕВНА <a id="logs"></a>

**Тип:** `string`     **По Умолчанию:** `'./'`

Расположение пользовательских журналов.

#### ONERROR <a id="onerror"></a>

**Тип:** `mixed`     **По Умолчанию:** `NULL`

Функция обратного вызова для использования в качестве пользовательского обработчика ошибок или `NULL`.**Обратите внимание:** если функция обратного вызова не указана, то создается страница ошибки по умолчанию \(HTML5 для синхронных запросов, строка JSON для AJAX-запросов\).

#### ONREROUTE <a id="onreroute"></a>

**Тип:** `mixed`     **По Умолчанию:** `NULL`

Функция обратного вызова, которая вызывается перед отправкой заголовков перенаправления. Поведение по умолчанию \(перенаправление 301/302\) будет обойдено, если `FALSE`не будет возвращено.

```text
$f3->set('ONREROUTE',function($url,$permanent){
  // do something
});
$f3->reroute('/foo?bar=baz');
```

#### ПАКЕТ <a id="package"></a>

**Тип:** `string|null`     **По Умолчанию:** `'Fat-Free Framework'`

Строка, содержащая `X-Powered-By`заголовок.

Если он пуст, то заголовок не отправляется.

#### PARAMS <a id="params"></a>

**Тип:** `array`     **По Умолчанию:** `array()`

Захваченные значения токенов определены в `route()`шаблоне. `PARAMS[0]` содержит захваченный URL-адрес относительно веб-корня.

#### ПУТЬ <a id="path"></a>

**Тип:** `string`, `Read-Only`

URL-адрес относительно базы. Значение по умолчанию: `parse_url($_SERVER['REQUEST_URI'],PHP_URL_PATH)`

#### ШАБЛОН <a id="pattern"></a>

**Тип:** `string`, `Read-Only`

Содержит шаблон маршрутизации, соответствующий текущему URI запроса.

#### ПЛАГИНЫ <a id="plugins"></a>

**Тип:** `string`     **По Умолчанию:** `__DIR__.'/'`

Расположение плагинов F3. Значение по умолчанию - это папка, в которой находится код фреймворка, то есть путь к `base.php`ней .

#### ПОРТ <a id="port"></a>

**Тип:** `integer`, `Read-Only`

Порт прослушивания TCP / IP, используемый веб-сервером. Значение по умолчанию: `$_SERVER['SERVER_PORT']`или `NULL`если оно недоступно.

#### ПРЕФИКС <a id="prefix"></a>

**Тип:** `string`     **По Умолчанию:** `NULL`

Префикс для использования с языком и локализациями.

Например, если ваш файл словаря содержит `hello = Hello World`, то термин будет доступен через:

* `$f3->get('hello')` без префикса
* `$f3->get('DICT.hello')` если `PREFIX=DICT.`\(обратите `.`внимание, это намеренно\)

**Важно:** эта переменная должна быть установлена _перед_ `LANGUAGE` и `LOCALES`.

#### ПРЕМАП <a id="premap"></a>

**Тип:** `string`     **Default:** `''`, пустая строка

Эта переменная позволяет создавать префиксы для обработчиков отображенных маршрутов. Например, определение:

```text
$f3->PREMAP = 'action_';
$f3->map('/item','Item');
```

это то же самое, что определение:

```text
$f3->route('GET /item','Item->action_get');
$f3->route('POST /item','Item->action_post');
$f3->route('PATCH /item','Item->action_patch');
$f3->route('PUT /item','Item->action_put');
$f3->route('DELETE /item','Item->action_delete');
```

#### ЗАПРОС <a id="query"></a>

**Тип:** `string`, `Read-Only`

Содержит строку запроса URI запроса запроса \(все после вопросительного знака`?`\).

#### ТИХИЙ <a id="quiet"></a>

**Тип:** `bool`     **По Умолчанию:** `FALSE`

Тумблер для подавления или включения стандартного вывода и сообщений об ошибках. Особенно полезно при [модульном тестировании](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/unit-testing).

#### СЫРОЙ <a id="raw"></a>

**Тип:** `bool`     **По Умолчанию:** `FALSE`

RAW должен быть верен при обработке больших данных, поступающих из `php://input`которых не поместится в памяти \(ср. [Тело](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/quick-reference#body)\).

#### ОБЛАСТЬ <a id="realm"></a>

**Тип:** `string`, `Read-Only`

Полный канонический URL-адрес. Значение по умолчанию: результат `'http(s)://'.$_SERVER['SERVER_NAME'].$_SERVER['REQUEST_URI']`

#### ОТВЕТ <a id="response"></a>

**Тип:** `string`, `Read-Only`

Тело последнего HTTP-ответа. F3 заполняет эту переменную независимо от `QUIET`настройки.

#### КОРЕНЬ <a id="root"></a>

**Тип:** `string`, `Read-Only`

Абсолютный путь к корневой папке документа.

#### МАРШРУТЫ <a id="routes"></a>

**Тип:** `array`     **По Умолчанию:** `array()`

Содержит определенные маршруты приложения.**Обратите внимание:** маршрут-это больше, чем просто URL-адрес. Это HTTP-глагол \(или глаголы\) и URL-адрес.

#### СХЕМА <a id="scheme"></a>

**Тип:** `string`, `Read-Only`

Серверный протокол. По умолчанию: `'http'`или `'https'`

#### СЕМЯ <a id="seed"></a>

**Тип:** `string`

Начальная строка используется в качестве префиксного имени для записей кэша и временных имен файлов, чтобы избежать коллизий ключей кэша. Если вы используете несколько доменов в своем приложении, то автоматически сгенерированное начальное значение будет отличаться по умолчанию. Если вы хотите совместно использовать общий кэш и хранилище временных файлов в обоих доменах, установите пользовательское начальное значение перед инициализацией кэша:

```text
$f3->set('SEED', $f3->hash('myDomainSEED'));
$f3->set('CACHE', TRUE);
```

**NB:** начальный ключ также используется для генерации токенов CSRF в обработчиках сеансов.

#### СЕРИАЛИЗАТОР <a id="serializer"></a>

**Тип:** `string`     **по умолчанию:** автоматически определяется

Сериализатор по умолчанию, используемый методом [Base - &gt;serialize \(\)&gt;](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#serialize). Значение по умолчанию: `igbinary`если доступно, в противном `php`случае .

#### ТЕМПЕРАТУРА <a id="temp"></a>

**Тип:** `string`     **По Умолчанию:** `'tmp/'`

Временная папка для кэша, блокировки файловой системы, скомпилированных шаблонов F3 и т. д. Значение по умолчанию-это `'tmp/'`папка внутри веб-корня. _Отрегулируйте соответствующим образом, чтобы соответствовать политикам безопасности вашего сайта_.

Если вы используете Google App Engine \(GAE\) для развертывания приложения, рекомендуется установить его в облачном хранилище dir.

#### ВРЕМЯ <a id="time"></a>

**Тип:** `float`     **по умолчанию:** автоматически определяется

Время начала работы фреймворка. Значение по умолчанию: текущее время Unix в секундах с точностью до ближайшей микросекунды в соответствии [с функцией PHP microtime\(\*\*TRUE\*\*\)](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/php.net/microtime).

#### ТЗ <a id="tz"></a>

**Тип:** `string`     **по умолчанию:** автоматически определяется

Часовой пояс для использования. Изменение этого значения автоматически вызывает базовую функцию PHP `date_default_timezone_set()`. Смотрите [список поддерживаемых часовых](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/php.net/manual/en/timezones.php) поясов, чтобы получить возможное значение для использования здесь. Падает обратно`'UTC'`, если автоматическое обнаружение не удается.

#### ПОЛЬЗОВАТЕЛЬСКИЙ ИНТЕРФЕЙС <a id="ui"></a>

**Тип:** `string`     **По Умолчанию:** `'./'`

Путь поиска файлов пользовательского интерфейса, используемых методом `View`and `Template`classes.`render()`  
Принимает трубу \(`|`\), запятую \(`,`\) или точку с запятой \(`;`\) в качестве разделителя для нескольких путей.

#### РАЗГРУЖАТЬ <a id="unload"></a>

**Тип:** `callback`     **По Умолчанию:** `NULL`

Определяет обработчик завершения работы, который фреймворк будет выполнять при [завершении работы приложения](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#unload).

#### ЗАГРУЗИТ <a id="uploads"></a>

**Тип:** `string`     **По Умолчанию:** `'./'`

Каталог, в котором сохраняются загружаемые файлы.

#### УРИ <a id="uri"></a>

**Тип:** `string`     **по умолчанию:** автоматически определяется

Ссылка на текущий URI HTTP-запроса.

#### ГЛАГОЛ <a id="verb"></a>

**Тип:** `string`     **по умолчанию:** автоматически определяется

Ссылка на текущий метод HTTP-запроса.

#### ВЕРСИЯ <a id="version"></a>

**Тип:** `string`     **по умолчанию:** например `'3.2.1-Release'`

Строка, содержащая версию фреймворка.

#### XFRAME <a id="xframe"></a>

**Тип:** `string|NULL`     **по умолчанию:** например `'SAMEORIGIN'`

Строка, содержащая `X-Frame-Options`заголовок.

Если он пуст, то заголовок не отправляется.

### Шаблонные Директивы <a id="template-directives"></a>

#### Знак <a id="token"></a>

* `@token`

  `@token`Заменить значением эквивалентной переменной F3.

* `{{ mixed expr }}`

  Оцените выражение `expr`. Выражение может включать шаблонные маркеры, константы, операторы \(унарные, арифметические, троичные и реляционные\), круглые скобки, преобразователи типов данных и функции. _Если это не атрибут шаблонной директивы, то результат будет echo'Ed_.

* `{{ string expr | esc }}`

  Выражение рендера `expr` _сбежало_. Это поведение фреймворка по умолчанию. `| esc`Суффикс необходим только в том случае, если глобальная переменная ESCAPE была установлена в `FALSE`значение.

* `{{ string expr | raw }}`

  Сделайте выражение `expr` _лица неоткрытым_. Поскольку F3 автоматически экранирует строковые маркеры по умолчанию, вы можете использовать этот суффикс для обхода экранирования конкретного маркера.

* `{{ string expr, arg0, …, argN | format }}`

  Визуализируйте выражение `expr`в ICU-формате и передайте аргументы, разделенные запятыми, где `arg0, …, argN`используется в `expr`качестве ссылки, каждый из которых имеет дополнительный форматер , который может быть одним из следующих:`'date'`,`'time'`, `'number'`или `'plural'`\(возможны дополнительные параметры форматирования\). Взгляните на метод [формата](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#format) для получения дополнительных примеров использования. Более подробная информация о [форматировании чисел, валют, дат и времени в отделении интенсивной](https://z5h64q92x9.net/proxy_u/en-ru.ru/userguide.icu-project.org/formatparse/)терапии . Образец: `{{'date: {0,date} - time: {0,time} - price: {1,number,currency}',time(),@price | format}}`

* `{{ string name, args | alias }}`

  создает URL-адрес именованного маршрута, т. е.: `{{ @name, 'a=5,b='.@id | alias }}`

* `{~ string expr ~}`

  Оценочное выражение `expr`, аналогичное `{{expr}}`результату, но не повторяющее его.

* `{* text-block *}`

  Исключите сегмент вашего шаблона. Псевдоним to `<exclude>`

* `{- {{@BASE}} -}`

  Игнорируйте все маркеры в `{- -}`выражении и печатайте их такими, какие они есть.

#### Включать <a id="include"></a>

```text
<include [ if="{{ bool condition }}" ] href="{{ string subtemplate }}" [ with="{{ string additional_variables }}" ] />
```

Получить содержимое `subtemplate`и вставить в текущую позицию шаблона \[ если опция `condition`имеет значение true\].

Текущий улей данных передается в подпланшет, обогащенный `additional_variables`if provided \(примеры см. [здесь](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/views-and-templates#templates-within-templates)\).

#### Исключать <a id="exclude"></a>

```text
<exclude>text-block</exclude>
```

Исключить `text-block`во время выполнения. Используется для встраивания комментариев в шаблоны. Псевдоним для этого есть:

```text
{* text-block *}
```

#### Игнорировать <a id="ignore"></a>

```text
<ignore>text-block</ignore>
```

Отображение `text-block`как есть, без какой-либо интерпретации/модификации шаблонизатором шаблонов.

#### Проверять <a id="check"></a>

```text
<check if="{{ bool condition }}">
    <true>true-block</true>
    <false>false-block</false>
</check>
```

Оцените `condition`. If`TRUE`, `true-block`то визуализируется; else `false-block`то визуализируется.

_**Короткая форма**_: Если вам не нужен и вы не указываете блок false, то для вашего удобства F3 делает открывающие и закрывающие теги для true необязательными:

```text
<check if="{{ @debugmode && @showtrace }}"><code>{{ @showtrace }}</code></check>
```

#### Петля <a id="loop"></a>

```text
<loop from="{{ statement }}" to="{{ bool expr }}" [ step="{{ statement }}" ]>
    text-block
</loop>
```

Оцените `from`заявление один раз. Проверьте, является ли выражение в `to`атрибуте `TRUE`оператором render `text-block`and evaluate`step`. Повторяйте итерацию до тех пор, пока `to`выражение не станет `FALSE`.

Пример:

```text
<loop from="{{ @i=0 }}" to="{{ @i < count(@bar) }}" step="{{ @i++ }}">

</loop>
```

#### Повторять <a id="repeat"></a>

```text
<repeat group="{{ array @group|expr }}" [ key="{{ scalar @key }}" ] value="{{ mixed @value }}" [ counter="{{ scalar @key }}" ]>
    text-block
</repeat>
```

Повторите `text-block`столько раз, сколько элементов в переменной массива `@group`или выражении `expr`. `@key` и `@value`функционировать таким же образом, как пара ключ-значение в эквивалентном PHP`foreach()`-операторе. Переменная, представленная `key`в `counter`атрибуте in, увеличивается `1`с каждой итерацией.

#### Переключатель <a id="switch"></a>

```text
<switch expr="{{ scalar expr }}">
    <case value="{{ scalar @value|expr }}" break="{{ bool TRUE|FALSE }}">
        text-block
    </case>
    .
    .
    .
    <default>
        message
    </default>
</switch>
```

Эквивалент структуры таблицы переходов PHP switch-case.

#### Набор <a id="set"></a>

```text
<!-- set some variables -->
<set foo="{{ 1+2 }}" bar="{{ @foo+3 }}" baz="xyz" />
<!-- set an array -->
<set myarray="{{ array('a','b','c') }}" />
```

Используется для динамической установки некоторых переменных в шаблоне.
