# Маршрутизация

Наш [первый пример](https://fatfreeframework.ru/user-guide/start) было не так уж трудно проглотить, не так ли? Вставьте другой маршрут перед командой `$f3->run()`:

```text
$f3->route('GET /about',
    function() {
        echo 'Donations go to a local charity... us!';
    }
);
```

Вы же не хотите загромождать глобальное пространство имен именами функций?   
Fat-Free распознает различные способы сопоставления обработчиков маршрутов с классами и методами ООП:

```text
class WebPage {
    function display() {
        echo 'I cannot object to an object';
    }
}

$f3->route('GET /about','WebPage->display');
```

HTTP-запросы также могут быть перенаправлены в статические методы класса:

```text
$f3->route('GET /login','Controller\Auth::login');
```

> Застрял с ошибкой 404? Проверьте конфигурацию [вашего сервера](https://fatfreeframework.com/3.7/routing-engine#sample-apache-configuration).

### Маршруты и токены 

В качестве демонстрации мощного доменного языка fat-Free \(DSL\) вы можете указать один маршрут для обработки различных возможностей:

```text
$f3->route('GET /brew/@count',
    function($f3) {
        echo $f3->get('PARAMS.count').' bottles of beer on the wall.';
    }
);
```

Этот пример показывает, как мы можем указать токен `@count`для представления части URL-адреса. Фреймворк будет обслуживать любой URL-адрес запроса, соответствующий префиксу `/brew/`, например `/brew/99`,  `/brew/98` и т. д. Это будет отображать `"99 бутылок пива на стене"` и `"98 бутылок пива на стене"`,  соответственно. 

Fat-Free также будет принять приглашение на странице /самогон/небьющиеся. \(Ожидайте, что это покажет "небьющиеся бутылки пива на стене".\) Когда такой динамический маршрут задан, Fat-Free автоматически заполняет глобальную переменную массива PARAMS значением захваченных строк в URL-адресе. Вызов $f3 - &gt;get \(\) внутри функции обратного вызова извлекает значение переменной фреймворка. Вы, конечно, можете применить этот метод в своем коде как часть презентации или бизнес-логики. Но мы обсудим это более подробно позже.

Обратите внимание, что Fat-Free понимает точечную нотацию массива. Вместо этого в коде можно использовать обычную нотацию PARAMS\['count'\], которая подвержена ошибкам опечаток и несбалансированным фигурным скобкам. В представлениях и шаблонах фреймворк допускает @PARAMS.графическая нотация, которая несколько похожа на Javascript. \(Мы рассмотрим представления и шаблоны позже.\)

Вот еще один способ доступа к маркерам в шаблоне запросов:

```text
$f3->route('GET /brew/@count',
    function($f3,$params) {
        echo $params['count'].' bottles of beer on the wall.';
    }
);
```

Вы можете использовать звездочку \( `*` \), чтобы принять любой URL-адрес после маршрута `/brew` - если вас действительно не волнует остальная часть пути:

```text
$f3->route('GET /brew/*',
    function() {
        echo 'Enough beer! We always end up here.';
    }
);
```

_Важный момент для рассмотрения: вы получите обезжиренный \(и себя\) путаете, если у вас есть оба `GET /brew/@count` и `GET /brew/*`_ вместе в одном приложении. Используйте одно или другое. Другое дело: Fat-Free видит `GET /brew` как отдельный и отличный от маршрута сделать `GET /brew/@count`. Каждый из них может иметь различные обработчики маршрутов.

`Важно: все обработчики маршрутов автоматически передают экземпляр фреймворка и маркеры маршрута. Видеть здесь.` 

### Именованные Маршруты 

Когда вы определяете маршрут, вы можете присвоить ему имя. Используйте имя маршрута в своем коде и шаблонах вместо введенного url-адреса. Затем, если вам нужно изменить свои URL-адреса, чтобы угодить маркетинговым властелинам, вам нужно только сделать изменение там, где был определен маршрут. Имена маршрутов должны соответствовать правилам именования переменных php \(без точек, тире и дефисов\).

Давайте назовем маршрут:

```text
$f3->route('GET @beer_list: /beer', 'Beer->list');
```

Имя вставляется после глагола маршрута \(`GET` в этом примере\), предшествующего символу `@`, и отделяется от части URL двоеточием `:` символ. Вы можете вставить пробел после двоеточия, если это облегчает чтение вашего кода \(как показано здесь\).

Чтобы перенаправить посетителя на новый URL-адрес, вызовите именованный маршрут внутри метода `reroute()`, например:

```text
// именованный маршрут - это строковое значение
$f3->reroute('@beer_list'); // обратите внимание на одинарные кавычки
```

 Если вы используете токены в своем маршруте, F3 заменит эти токены их текущим значением. Если вы хотите изменить значение маркера перед вызовом reroute, передайте его в качестве 2-го аргумента:

```text
$f3->route('GET @beer_list: /beer/@country', 'Beer->bycountry');

// в качестве аргумента именованному маршруту передается набор пар ключ-значение
$f3->reroute('@beer_list(@country=Germany)');

// если в вашем маршруте требуется более одного токена
$f3->route('GET @beer_village_list: /beer/@country/@village', 'Beer->byvillage');
$f3->reroute('@beer_village_list(@country=Germany,@village=Rhine)');
```

Не забудьте указать `urlencode()` ваши аргументы, если у вас есть символы, которые не соответствуют рекомендациям RFC 1738 для хорошо сформированных URL-адресов.

### Именованные маршруты в шаблонах 

Чтобы получить доступ к именованному маршруту в шаблоне, можно передать имя маршрута в фильтр псевдонимов:

```text
<a href="{{ 'beer_list' | alias }}">view beer list</a>
```

Если вы хотите построить ссылку на именованный маршрут, содержащий маркеры, вы можете передать дополнительные параметры в фильтр псевдонимов:

```text
<a href="{{ 'beer_village_list', 'country=Germany,village=Rhine' | alias }}">view beer list from Rhine, Germany</a>
```

Это также работает с переменными, которые выглядят как . Вам нужно только установить или перезаписать маркеры в именованных маршрутах, которые вам нужно изменить. Все остальные маркеры разрешаются автоматически, исходя из текущего маршрута.

Если вам нужно заменить маркеры подстановочных знаков в вашем маршруте \(т. е. получить @complex:/resize/@format/ _/ sep/_\), используйте числовой индекс, чтобы указать его новое значение, например: .

Чтобы создать URL-адреса в коде контроллера, см. раздел псевдоним и методы сборки.

Динамические Веб-Сайты Подождите секунду - во всех предыдущих примерах мы никогда по-настоящему не создавали каталог на нашем жестком диске для хранения этих маршрутов. Короткий ответ: мы не должны этого делать. Все маршруты F3 являются виртуальными. Они не отражают нашу структуру папок на жестком диске. Если у вас есть программы или статические файлы \(изображения, CSS и т. д.\) которые не используют фреймворк - до тех пор, пока пути к этим файлам не конфликтуют с каким - либо маршрутом, определенным в вашем приложении-ваше программное обеспечение веб-сервера доставит их в браузер пользователя, если сервер настроен правильно.

Встроенный веб-сервер PHP 5.4 Последняя стабильная версия PHP имеет свой собственный встроенный веб-сервер. Запустите его, используя следующую конфигурацию:

PHP-S localhost:80-t /var / www/ Приведенная выше команда начнет маршрутизацию всех запросов в web root /var / www. Если поступит входящий HTTP-запрос на файл или папку, PHP будет искать его в корне веб-сайта и отправлять в браузер, если он будет найден. В противном случае PHP загрузит индекс по умолчанию.php \(содержащий ваш код с поддержкой F3\).

Пример Конфигурации Apache Если вы используете Apache, убедитесь, что вы активировали модуль перезаписи URL-адресов \(mod\_rewrite\) в вашем apache.conf \(или httpd.conf\) файл. Вы также должны создать a .файл htaccess, содержащий следующее:

RewriteEngine On

RewriteRule ^\(app\|dict\|ns\|tmp\)\|/.ini$ - \[R=404\]

RewriteCond %{REQUEST\_FILENAME} !-л RewriteCond %{REQUEST\_FILENAME} !-Ф RewriteCond %{REQUEST\_FILENAME} !-д Переписывай все заново . _индекс.php \[L, QSA\] Переписывай все заново ._ - \[E=HTTP\_AUTHORIZATION:%{HTTP:Authorization}, L\] Скрипт сообщает Apache, что всякий раз, когда приходит HTTP-запрос и если нет физического файла \(!-f\) или путь \(!- г\) или символическая ссылка \(!-л\) можно найти, он должен передать управление индексу.php, который содержит наш главный / передний контроллер, и который, в свою очередь, вызывает фреймворк.

То.файл htaccess, содержащий указанные выше директивы Apache, всегда должен находиться в той же папке, что и index.РНР.

Вам также нужно настроить Apache, чтобы он знал физическое местоположение индекса.php на вашем жестком диске. Типичная конфигурация-это:

DocumentRoot "/var / www / html" &lt;Каталог "/var/www / html"&gt; Опции-Индексы + FollowSymLinks +Включает В Себя AllowOverride Все Приказ разрешить, запретить Разрешить от всех &lt;/Справочник&gt; В случае, если вы просто помещаете свой обезжиренный проект в подпапку существующего корневого документа, некоторые конфигурации Apache, возможно, также нуждаются в определенной базе перезаписи .файл htaccess. Если приложение не работает или маршрут по умолчанию / работает, но /test, возможно, не работает, попробуйте добавить базовый путь:

RewriteEngine On RewriteBase /fatfree-проект/ Если вы разрабатываете несколько приложений одновременно, управлять конфигурацией виртуального хоста будет проще:

NameVirtualHost  _&lt; VirtualHost_ &gt; Имя_сервера site1.com DocumentRoot "/var / www / site1" &lt;Каталог "/var/www / site1"&gt; Опции-Индексы + FollowSymLinks +Включает В Себя AllowOverride Все Приказ разрешить, запретить Разрешить от всех &lt;/Справочник&gt; &lt; / VirtualHost&gt; &lt; VirtualHost \*&gt; Имя_сервера site2.com DocumentRoot "/var / www / site2" &lt;Каталог "/var/www / site2"&gt; Опции-Индексы + FollowSymLinks +Включает В Себя AllowOverride Все Приказ разрешить, запретить Разрешить от всех &lt;/Справочник&gt; &lt; / VirtualHost&gt; Каждое имя сервера \(site1.com и еще site2.com в нашем примере\) должен быть указан в вашем файле /etc/hosts. В Windows вы должны отредактировать C:/WINDOWS/system32/drivers/etc/hosts-да. Для осуществления изменений может потребоваться перезагрузка компьютера. Затем вы можете направить свой веб-браузер по указанному адресу [http://site1.com](http://site1.com) или [http://site2.com](http://site2.com) виртуальные хосты значительно облегчают развертывание ваших приложений.

Альтернативная конфигурация Apache Если mod\_rewrite недоступен на вашем сервере или вы хотите немного повысить производительность, вы можете воспользоваться директивой FallbackResource, доступной в Apache 2.4 и выше.

В таком случае просто добавьте следующую строку в свой список .файл htaccess или в вашей конфигурации VirtualHost:

FallbackResource /индекс.РНР Если ваше веб-приложение работает в подпапке корневого сервера, не забудьте соответствующим образом изменить директиву:

FallbackResource /fatfree-проект/Индекс.РНР Обратите внимание: в настоящее время существует ошибка, которая пропускает директиву, когда запрошенный URI заканчивается.php и находится в корне приложения: [http://localhost/fatfree-project/foo-да.php](http://localhost/fatfree-project/foo-да.php) = &gt; ошибка 404 [http://localhost/fatfree-project/foo/bar-да.php](http://localhost/fatfree-project/foo/bar-да.php) = &gt; OK Пример Конфигурации Nginx
