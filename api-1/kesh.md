# Кэш

Этот класс представляет собой многопротокольный кэш-движок F3. Он поддерживает [memcache](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/memcached.org/), [WinCache](https://z5h64q92x9.net/proxy_u/en-ru.ru/www.iis.net/downloads/microsoft/wincache-extension), [APC](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/php.net/manual/en/book.apc.php), [XCache](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/xcache.lighttpd.net/), [Redis](https://z5h64q92x9.net/proxy_u/en-ru.ru/redis.io/) и кэширование на основе файловых систем.

Кэширование-это мощный способ повысить производительность вашего приложения. Он легко интегрируется со всеми основными и плагинными функциями например[, установка ключей улья](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#set), [HTTP-ответов](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/base#caching), картографов БД и запросов и даже [минификация JS/CSS](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/optimization#keeping-javascript-and-css-on-a-healthy-diet).

Существует хорошее [руководство пользователя Cache Engine](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/fatfreeframework.com/3.7/optimization#cache-engine), которое описывает, как работает Cache engine, и дает вам советы по улучшению вашего приложения и запросов к базе данных, поскольку они также могут быть кэшированы с помощью F3. Тебе действительно надо было прочитать его.

Пространство имен: `\`  
Расположение файла: `lib/base.php`

#### Экземпляр <a id="instantiation"></a>

**Возвращаемый экземпляр класса**

```text
$cache = \Cache::instance();
```

Механизм кэширования по умолчанию отключен. Чтобы активировать его с помощью автоматически обнаруженного бэкенда кэша, просто установите значение VAR системы кэша `TRUE`. Дополнительную конфигурацию смотрите в приведенной ниже функции load \(\).

Класс кэша использует заводскую оболочку Prefab, поэтому вы можете захватить один и тот же экземпляр этого класса в любой точке вашего кода.

#### набор <a id="set"></a>

**Храните пару ключ / значение в кэше**

```text
mixed|FALSE set( string $key, mixed $val [, int $ttl = 0 ] )
```

Если `$ttl`равно 0, то запись сохраняется в течение бесконечного времени. В противном случае указанное время в секундах используется как TTL.**НБ:** Версии F3 старше 3.6.5 повторно используют старые сроки годности. Начиная с 3.6.5, повторно используются только старые значения TTL. См. \[выпуск №237\] \(https://github.com/bcosca/fatfree-core/issues/237\) подробнее.

#### существует <a id="exists"></a>

**Проверьте, существует ли запись кэша: верните метку времени и TTL записи кэша или FALSE, если она не найдена**

```text
array|FALSE exists( string $key [, mixed &$val = NULL ] )
```

Возвращает массив, содержащий элементы \[0\] creation timestamp и \[1\] time-to-live \(TTL, в секундах\) записи кэша; или `FALSE`если она не была найдена.

```text
$cache->set('foo','bar',5);

var_dump($cache->exists('foo'));
/* returns
array(2) {
  [0]=>  float(1369257446.4874)
  [1]=>  int(5)
}
*/
```

Вы также можете использовать этот `$val`аргумент для извлечения содержимого записи кэша. Это может сохранить дополнительный `get`вызов к бэкэнду кэша.

```text
if ($cache->exists('foo',$value)) {
    echo $value; // bar
}
```

#### получить <a id="get"></a>

**Извлечение значения записи кэша**

```text
mixed|FALSE get( string $key )
```

#### Очистить <a id="clear"></a>

**Удаление записи кэша**

```text
bool clear( string $key )
```

#### Сброс <a id="reset"></a>

**Очистить содержимое бэкэнда кэша**

```text
bool reset( [ string $suffix = NULL ] )
```

Вы можете использовать `$suffix`для очистки записей кэша только тот суффикс, который соответствует этой строке.

Вы также можете использовать `$f3->clear('CACHE')`его в качестве ярлыка для этого.**Ограничения:**

* при использовании XCache все записи очищаются, независимо от их значения `$suffix`.
* при использовании Memcached через расширение [memcache](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/pecl.php.net/package/memcache), _статистика_ cachedump должна быть включена \(без `-X`флага\), иначе команда не будет работать. Расширение [memcached](https://z5h64q92x9.net/proxy_u/en-ru.ru/https/pecl.php.net/package/memcached) у него нет этого ограничения.

#### загружать <a id="load"></a>

**Загрузка / автоматическое определение бэкенда кэша. Возврат кэша DSN**

```text
string load( string|bool $dsn )
```

Возможные конфигурации для `$dsn`are:

* `apc`
* `apcu`
* `wincache`
* `xcache`
* `memcache=localhost:11211`
* `memcached=localhost:11211`
* `redis=localhost`
* `folder=tmp/cache/`

Вы можете установить `$dsn`значение `TRUE`для запуска функции автоматического обнаружения, используя файловую систему в качестве запасного варианта, когда механизм общей памяти не был обнаружен или недоступен. Установка `$dsn`на `FALSE`отключение кэширования.

Бэкенды `memcache`and `memcached`поддерживают несколько серверов. Серверы должны быть разделены на`,`, `;`или `|`.

Возвращает DSN кэша используемого кэша \(заданный или автоматически обнаруженный\).

