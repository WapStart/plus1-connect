#API для подключения к plus1.wapstart

##1. Вступление


В документе описано API для подключения мобильных сайтов и приложений к рекламной сети Plus1.

Для подключения к рекламной сети Plus1 необходимо выполнить GET запрос к серверу рекламной сети 
и разобрать ответ от него согласно данной спецификации


**Примечание:**

Вы так же можете воспользоваться уже готовым набором инструментов для подключения к Plus1:

 * [sdk для устройств под управлением android](https://github.com/WapStart/plus1-android-sdk)
 * [sdk для устройств под управление iOs](https://github.com/WapStart/plus1-ios-sdk)
 * [javascript код для мобильных сайтов](https://github.com/WapStart/plus1-js-sdk)
 * [реализация интеграции server-to-server на php](https://github.com/WapStart/plus1-php-sdk)
 * [реализация интеграции server-to-server на perl](git@github.com:WapStart/plus1-perl-sdk.git)
 * [код для веб-сайтов, который показывает рекламу только для мобильных пользователей (php + js)](https://github.com/WapStart/plus1-web-sdk)

Данные инструменты доступны в панели управления вашего аккаунта по адресу **http://plus1.wapstart.ru/**.

##2. URL для запроса

Запрос к серверу выдачи объявлений идет через хост: **http://ro.plus1.wapstart.ru/**

*Мы настоятельно советуем использовать именно имя хоста, не IP.*

##3. Форматы рекламных объявлений

Рекламная сеть Plus1 позволяет размещать текстовые и графические баннеры и их
комбинации по стандартам [MMA](http://www.mmaglobal.com/mobileadvertising.pdf).

В текстовом объявлении обязательно присутствует кликабельный заголовок (длиной до 35 символов), 
а также может присутствовать дополнительный поясняющий текст(длиной до 35 символов).Графический баннер доступен в формате GIF (в том числе анимированный), JPG, PNG.

##4. Формирование запроса

Контроллеры, обрабатывающие запрос, делятся на два типа и имеют различные форматы ответа:

- **direct**, показ рекламы на клиенте (JavaScript или мобильные приложения), форматы ответа: js, xml, html

- **proxy**, веб-сервис для интеграции server-to-server с рекламной сетью Plus1, форматы ответа: json, xml, html

В общем случае URI для GET-запроса выглядит следующим образом: /:type/:format/id/:id, где
- *type* - тип контроллера
- *format* - формат ответа
- *id* - идентификатор площадки в сети Plus1

Примеры URI:
- /direct/js/id/493
- /direct/xml/id/8
- /direct/html/id/493
- /proxy/json/id/8
- /proxy/xml/id/493
- /proxy/html/id/8

В зависимости от типа контроллера, а также от формата ответа необходимо передать набор дополнительных параметров двумя способами:

- в query_string запроса

- в дополнительных заголовках запроса.

###4.1 Дополнительные параметры для контроллеров типа direct


#### 4.1.1 Параметры, которые необходимо передать в query_string запроса

* *pageId* - идентификатор показа на странице(строка, длиной 40 символов, содержащая только латинские буквы и цифры).
В рамках одного pageId ротатор будет пытаться найти разные баннеры.
Для показа нескольких баннеров на одном экране вы должны сделать запросы с одинаковыми pageId. Обязательный параметр.

* *encoding* - идентификатор кодировки. 1 для utf-8 и 2 для cp1251. Не обязательный параметр. По-умолчанию 1.

* *sex* - пол пользователя. 1 для мужчин и 2 для женщин. Необязательный параметр.

* *age* - возраст пользователя. Число полных лет. Необязательный параметр.

* *kws* – Ключевые слова пользователя. URL encoded строка, содержащая значения, разделенные запятыми. Необязательный параметр.

* *login* - логин, либо другой идентификатор пользователя. Строка. 
В целях сохранения конфиденциальности личной информации мы рекомендуем передавать не логин, а результат хеширующей функции от него. Необязательный параметр.

* *location* - географические координаты пользователя в формате WGS-84. В параметре через точку с запятой указываются долгота и широта (в градусах), например, location=37.677751;55.757718
Необязательный параметр.

Все параметры, характеризующие устройство (бренд/модель/операционную систему), должны быть
переданы дополнительными заголовками.

#### 4.1.2 Параметры, которые необходимо передать в дополнительных заголовках запроса

Обязательные:

* *User-Agent* - подчиняется стандартным правилам

* *Cookies* - требуется передавать cookie, которую ротатор пытался поставить при первом запросе

* *Host: ro.plus1.wapstart.ru* - этого требует http/1.1

Необязательные:

* *x-display-metrics* - разрешение экрана в формате "1024x768".

* *x-application-type* - в общем случае название платформы. Строка с произвольным содержимым.

* *x-device-imei* - imei номер устройства. Целое число.

* *x-preferred-locale* - язык пользователя. Строка.

Так же можно передать любые другие заголовки, которые позволят более точно таргетировать пользователя, предварительно согласовав их с администрацией Plus1.

###4.2 Дополнительные параметры для контроллеров типа proxy
----------------------------

#### 4.2.1 Параметры, которые необходимо передать в query_string запроса

* *pageId* - идентификатор показа на странице(строка, длиной 40 символов, содержащая только латинские буквы и цифры).
В рамках одного pageId ротатор будет пытаться найти разные баннеры.
Для показа нескольких баннеров на одном экране вы должны сделать запросы с одинаковыми pageId. Обязательный параметр.

* *ip* - ip4 адрес клиента. Обязательный параметр.

* *encoding* - идентификатор кодировки. 1 для utf-8 и 2 для cp1251. Не обязательный параметр. По-умолчанию 1.

* *sex* - пол пользователя. 1 для мужчин и 2 для женщин. Необязательный параметр.

* *age* - возраст пользователя. Число полных лет. Необязательный параметр.

* *kws* – Ключевые слова пользователя. URL encoded строка, содержащая значения, разделенные запятыми. Необязательный параметр.

* *login* - логин, либо другой идентификатор пользователя. Строка. В целях сохранения конфиденциальности личной информации мы рекомендуем передавать не логин, а результат хеширующей функции от него. Необязательный параметр.

* *geoData* - строка с гео-информацией (город, штат, страна разделенные знаком “;”). Если эта информация доступна, пожалуйста уведомите администрацию сети для окончательного согласования
формата. Необязательный параметр.

* *countryCode* – Код страны конечного пользователя в формате ISO-3166-1 (2 латинские буквы). Необязательный параметр.

* *markup* - тип разметки 3 для xhtml и 2 для wml. По умолчанию 3. Необязательный параметр. *только для формата ответа html*

* *bannerAmount* - число баннеров. Беззнаковое целое от 1 до 3. Необязательный параметр. По умолчанию 1.

#### 4.2.2 Параметры, которые необходимо передать в дополнительных заголовках запроса

Все заголовки пользователя, начинающиеся с "accept-" и "x-" а так же "host", "referer", "via",
требуется передать вместе с запросом в API, добавив перед именем заголовка подстроку "x-plus1-".

Дополнительно нужно передать в ротатор заголовок "x-plus1-remote-addr", поместив в его значение ip адреса клиента (remote-addr).

Значение заголовка User-Agent требуется продублировать в этом же заголовке без изменений(т.е. передать и x-plus1-user-agent и user-agent).

Обеспечение определения уникальности пользователя:

По возможности старайтесь обеспечивать API сведениями об уникальности пользователей. Для этого можно использовать два механизма:

* передача параметра login

* передача в ротатор Cookie с именем wssid. При этом установку куки конечному клиенту выдолжны обеспечить своими силами.

В xml баннере есть нода с именем cookieSetterUrl.
В ее содержимом находится картинка, которую надо "обязать" загрузить пользователя.Как правило, для этого надо просто вывести ее в html.
В обычном (html) баннере тэг с этой картинкой присутствует по-умолчанию.

##5. Порядок разбора ответа

![Порядок разбора ответа API](https://github.com/WapStart/plus1-connect/raw/master/doc/ru-api-response.jpg)

##6. Примеры формирования запроса и получения ответа

###6.1 Запрос к контроллеру direct c получением ответа в формате xml

Запрос:

    GET /direct/xml/id/8/?sex=1&age=42&kws=russia%2Cmobile%20adv%2Cwapstart&pageId=01234&location=42;43 HTTP/1.1
    Host: ro.plus1.wapstart.ru
    User-agent: Android, linux, htc hero
    Cookies: wssid=e1f9c9ebab3014bc65940ab812c8629bf25dd86e
    x-display-metrics: 480x320
    \r\n\r\n

Ответ:

    HTTP/1.1 200 OK
    Server: nginx
    Date: Tue, 28 Jun 2011 10:57:27 GMT
    Content-Type: text/xml; charset=utf-8
    Transfer-Encoding: chunked
    Connection: keep-alive
    Keep-Alive: timeout=20
    Set-Cookie: wssid=e1f9c9ebab3014bc65940ab812c8629bf25dd86e; expires=Thu, 28-Jul-2011 10:57:27GMT; path=/; domain=.wapstart.ru
    Cache-control: no-cache
    Expires: Tue, 28 Jun 2011 13:57:27 GMT
    <?xml version="1.0" encoding="UTF-8"?>
    <banner>
      <id>11864</id>
      <title>Новый мобильный сайт AVITO.ru!</title>
      <content>Узнай сколько стоит твой беспорядок</content>
      <responseType>1</responseType>
      <link>http://ro.plus1.wapstart.ru/index.php?area=redirector&amp;type=1&amp;rsId=at_72f35e9b58bc67437e73feafd22854a59597d83c_281457&amp;site=8&amp;banner=11864&amp;usr=e1f9c9</link>
      <pictureUrl></pictureUrl>
      <pictureUrlPng></pictureUrlPng>
    </banner>


В данном случае сервер нашел баннер, подходящий под этот запрос и вернул в ответ xml, содержащую следующие поля:

* *id* - идентификатор баннера в plus1 (справочно)
* *title* - заголовок баннера
* *content* - текст баннера
* *responseType* - тип обратной связи. На момент написания документа используется только 1, что означает "переход по ссылке"
* *link* - ссылка, которую нужно открыть в стандартном браузере при клике пользователя по баннеру
* *pictureUrl* - url с картинкой баннера
* *pictureUrlPng* - url с картинкой баннера (копией) в формате png

###6.2 Запрос к контроллеру proxy c получением ответа в формате html

Запрос:

    GET /proxy/html/id/2562/?pageId=7bf4a3975516ea84f4c1c5893019d581b227e731
    &ip=83.149.32.132&encoding=1&sex=2&age=36&kws= russia%2Cmobile%20adv%2Cwapstart
    &geoData=a%3A2%3A%7Bs%3A2%3A%22id%22%3Bs%3A4%3A%222562%22%3Bs%3A7%3A%22geoData%22%3Bs%3A4%3A%225393%22%3B%7D HTTP/1.1
    Host: ro.plus1.wapstart.ru
    User-Agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
    x-plus1-user-agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
    x-plus1-remote-addr: 83.149.32.132

Ответ:

    HTTP/1.1 200 OK
    Server: nginx
    Date: Tue, 28 Jun 2011 11:51:15 GMT
    Content-Type: text/html; charset=utf-8
    Transfer-Encoding: chunked
    Connection: keep-alive
    Keep-Alive: timeout=20

    <div style="background-color: #ffffff; border: solid 1px #a4cef2; padding: 5px; margin: 0px 0px 0px 0px;
    font-family: Arial, Helvetica, sans-serif;">
    <div style="border-bottom: 1px solid #3591c8; height: 10px;"><img style="margin: 0px; padding:
    0px; vertical-align: top; border: none;" alt="#" width="58" height="10"
    src="http://ro.plus1.wapstart.ru/banner_images/static/reklame/5.gif"/></div>
    <p style="margin: 0px 0px 0px 0px; padding: 0px 0px 0px 0px;">
    <a href="http://ro.plus1.wapstart.ru/index.php?area=redirector&amp;type=1&amp;rsId=hr_25ec7e658562
    89c912c8a6ee80e46ae64807b73c_281551&amp;site=2562&amp;banner=11660&amp;usr=b944c2"
    style="color: #0e3fcc; margin :0px 0px 0px 0px; padding: 0px 0px 0px 0px; text-decoration:
    underline;">Соблазнительный кредит от Банк24.ру</a>
    <br />
    <span style="color: #000000; line-height: 11px; margin: 0px 0px 0px 0px; padding: 4px 0px 3px
    0px;">Оформи заявку on-line!</span>
    </p>
    </div>
    <img
    src="http://ro.plus1.wapstart.ru//?area=counter&amp;clientSession=c9101a8da8de89d9f93357b061577
    8dc314eef18&amp;bannerId=11660&amp;site=2562" alt="" width="1px" height="1px" style="border: 0;
    position: absolute;" />
    <!-- i4jgij4pfd4ssd -->

###6.3 Запрос к контроллеру proxy c получением ответа в формате xml

Запрос:

    GET proxy/html/id/2562/?pageId=7bf4a3975516ea84f4c1c5893019d581b227e731
    &ip=83.149.32.132&encoding=1&sex=2&age=36&kws=russia%2Cmobile%20adv%2Cwapstart
    &geoData=a%3A2%3A%7Bs%3A2%3A%22id%22%3Bs%3A4%3A%222562%22%3Bs%3A7%3A%22geoData%22%3Bs%3A4%3A%225393%22%3B%7D HTTP/1.1
    Host: ro.plus1.wapstart.ru
    User-Agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
    x-plus1-user-agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
    x-plus1-remote-addr: 83.149.32.132
    \r\n\r\n

Ответ:

    HTTP/1.1 200 OK
    Server: nginx
    Date: Mon, 04 Jul 2011 08:00:06 GMT
    Content-Type: text/xml; charset=utf-8
    Transfer-Encoding: chunked
    Connection: keep-alive
    Keep-Alive: timeout=20
    X-Powered-By: PHP/5.3.3-1
    Cache-control: no-cache
    Expires: Mon, 04 Jul 2011 11:00:06 GMT

    <?xml version="1.0" encoding="UTF-8"?>
    <banner>
      <id>11864</id>
        <title>Новый мобильный сайт AVITO.ru!</title>
        <content>Узнай сколько стоит твой беспорядок</content>
        <link>http://ro.plus1.wapstart.ru/index.php?area=redirector&amp;type=1&amp;rsId=at_f42938923cbe508082fcc174d9add416371b57e9_041200&amp;site=2562&amp;banner=11864&amp;usr=99895f</link>
        <pictureUrl></pictureUrl>
        <cookieSetterUrl>http://ro.plus1.wapstart.ru//?area=counter&amp;clientSession=c9101a8da8de89d9f93357b0615778dc314eef18&amp;bannerId=11864&amp;site=2562</cookieSetterUrl>
        <singleLineContent>Новый мобильный сайт AVITO.ru!</singleLineContent>
    </banner>

В данном случае наш сервер нашел баннер, подходящий под этот запрос и вернул в ответ xml,
содержащую следующие поля:
* *id* - идентификатор баннера в plus1 (справочно)
* *title* - заголовок баннера
* *content* - текст баннера
* *link* - ссылка, которую нужно открыть в стандартном браузере при клике пользователя по баннеру
* *pictureUrl* - url с картинкой баннера
* *cookieSetterUrl* – картинка для установки кук пользователю
* *singleLineContent* – текст данного объявления 1ой строкой (доступен только у текстовых объявлений)
