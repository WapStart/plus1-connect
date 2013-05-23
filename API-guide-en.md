# Plus1 ad network connection

##1. Introduction

This document provides API to connect mobile sites and application to Plus1 ad network.

On order to connect to Plus1 ad network a site needs to generate HTTP GET query to ad network server
and parse response from it according to this document.


**Note:**

You can also use our tools to connect to Plus1 ad network:

 * [android sdk](https://github.com/WapStart/plus1-android-sdk)
 * [iOs sdk](https://github.com/WapStart/plus1-ios-sdk)
 * [javascript code for mobile sites](https://github.com/WapStart/plus1-js-sdk)
 * [web-service integration (php)](https://github.com/WapStart/plus1-php-sdk)
 * [web-service integration (perl)](https://github.com/WapStart/plus1-perl-sdk)
 * [code for web-sites, which shows ads only for mobile users (php + js)](https://github.com/WapStart/plus1-web-sdk)

These tools are available in your account settings at **https://plus1.wapstart.ru/**.

##2. Request URL

Request URL goes through host: **ro.plus1.wapstart.ru**

*We recommend using hostname, not IP address*

##3. Banner ad specifications

Plus1 ad network works with text and graphic banners and theirs combinations according to [MMA standard](http://www.mmaglobal.com/mobileadvertising.pdf).

Text ad must have clickable header (maximum length of 35 characters), and also may have additional text (maximum length of 35 characters). Graphic banners are available in PNG, JPG, GIF (may be animated)

##4. Request

Controllers are divided into two types and have different response formats:

- **direct**, used for showing ads on clinet (JavaScript or mobile applications), response formats: js, xml, html

- **proxy**, Plus1 ad network web-service, response formats: json, xml, html

Request URI for GET query looks as follows: **/:type/:format/id/:id**, where

- *type* - controller type

- *format* - response format

- *id* - site identifier in Plus1 ad network

URI examples:

- /direct/js/id/493

- /direct/xml/id/8

- /direct/html/id/493

- /proxy/json/id/8

- /proxy/xml/id/493

- /proxy/html/id/8

Depending on the controller type and the response format it's necessary to transmit additional parameters in two ways:

- in query_string in GET query

- in additional request headers

###4.1 Additional parameters for direct controllers

#### 4.1.1 Query string parameters

<table>
	<tr>
		<th>Parameter name</th>
		<th>Parameter description</th>
		<th>Parameter type</th>
		<th>Parameter format</th>
		<th>Default value</th>
		<th>Required</th>
	</tr>
	<tr>
		<td>pageId</td>
		<td>Ad-cast unique identifier. Within one pageId the rotator will try to find different banners. To show several different banners on one screen you should make requests with the same pageId</td>
		<td>String</td>
		<td>[0-9a-f]{40}</td>
		<td>No</td>
		<td>Yes</td>
	</tr>
	<tr>
		<td>version</td>
		<td>API version</td>
		<td>Integer</td>
		<td>[1|2]</td>
		<td>2</td>
		<td>No</td>
	</tr>
	<tr>
		<td>encoding</td>
		<td>Encoding identifier. 1 - utf-8, 2 - cp-1251</td>
		<td>Integer</td>
		<td>[1|2]</td>
		<td>1</td>
		<td>No</td>
	</tr>
	<tr>
		<td>sex</td>
		<td>User sex. 1 - male, 2 - female</td>
		<td>Integer</td>
		<td>[1|2]</td>
		<td>No</td>
		<td>No</td>
	</tr>
    <tr>
		<td>age</td>
		<td>User age</td>
		<td>Integer</td>
		<td>[1-9][0-9]</td>
		<td>No</td>
		<td>No</td>
	</tr>
	<tr>
		<td>kws</td>
		<td>User keywords</td>
		<td>String</td>
		<td>urlencoded, keywords must be divided by comma</td>
		<td>No</td>
		<td>No</td>
	</tr>
	<tr>
		<td>login</td>
		<td>User identifier. In order to keep user information secure we recommend to transmit hash function result for user identifier</td>
		<td>String</td>
		<td>[0-9a-f]{40}</td>
		<td>No</td>
		<td>No</td>
	</tr>
	<tr>
		<td>location</td>
		<td>User location</td>
		<td>String</td>
		<td>WGS_84. Latitude and longitude must be divided by semicolon</td>
		<td>No</td>
		<td>No</td>
	</tr>
</table>

#### 4.1.2 Additional headers parameters

All parameters that descibe device (brand/model/operating system) must be transmitted as additional headers.

<table>
	<tr>
		<th>Header name</th>
		<th>Header description</th>
		<th>Required</th>
	</tr>
	<tr>
		<td>Host</td>
		<td>Must be "ro.plus1.wapstart.ru", HTTP 1.1 requirement</td>
		<td>Yes</td>
	</tr>
	<tr>
		<td>User-Agent</td>
		<td>Browser User-Agent</td>
		<td>Yes</td>
	</tr>
    <tr>
		<td>Cookies</td>
		<td>Cookies that ad network server tried to set in first query</td>
		<td>Yes</td>
	</tr>
	<tr>
		<td>x-display-metrics</td>
		<td>screen resolution in format "1024x768"</td>
		<td>No</td>
	</tr>
	<tr>
		<td>x-application-type</td>
		<td>In common case platform name</td>
		<td>No</td>
	</tr>
	<tr>
		<td>x-device-imei</td>
		<td>Device IMEI</td>
		<td>No</td>
	</tr>
	<tr>
		<td>x-preferred-locale</td>
		<td>User language</td>
		<td>No</td>
	</tr>
</table>

Also you may transmit any other headers, which allows to target user more accurately after ad network administration agreement

###4.2 Additional parameters for proxy controller
----------------------------

#### 4.2.1 Query string parameters

<table>
	<tr>
		<th>Parameter name</th>
		<th>Parameter description</th>
		<th>Parameter type</th>
		<th>Parameter format</th>
		<th>Default value</th>
		<th>Required</th>
	</tr>
	<tr>
		<td>ip</td>
		<td>User IP address</td>
		<td>String</td>
		<td>ipv4</td>
		<td>No</td>
		<td>Yes</td>
	</tr>
	<tr>
		<td>pageId</td>
		<td>Ad-cast unique identifier. Within one pageId the rotator will try to find different banners. To show several different banners on one screen you should make requests with the same pageId</td>
		<td>String</td>
		<td>[0-9a-f]{40}</td>
		<td>No</td>
		<td>Yes</td>
	</tr>
	<tr>
		<td>version</td>
		<td>API version</td>
		<td>Integer</td>
		<td>[1|2]</td>
		<td>2</td>
		<td>No</td>
	</tr>
	<tr>
		<td>encoding</td>
		<td>Encoding identifier. 1 - utf-8, 2 - cp-1251</td>
		<td>Integer</td>
		<td>[1|2]</td>
		<td>1</td>
		<td>No</td>
	</tr>
	<tr>
		<td>sex</td>
		<td>User sex. 1 - male, 2 - female</td>
		<td>Integer</td>
		<td>[1|2]</td>
		<td>No</td>
		<td>No</td>
	</tr>
	<tr>
		<td>age</td>
		<td>User age</td>
		<td>Integer</td>
		<td>[1-9][0-9]</td>
		<td>No</td>
		<td>No</td>
	</tr>
	<tr>
		<td>kws</td>
		<td>User keywords</td>
		<td>String</td>
		<td>urlencoded, keywords must be divided by comma</td>
		<td>No</td>
		<td>No</td>
	</tr>
	<tr>
		<td>login</td>
		<td>User identifier. In order to keep user information secure we recommend to transmit hash function result for user identifier</td>
		<td>String</td>
		<td>[0-9a-f]{40}</td>
		<td>No</td>
		<td>No</td>
	</tr>
	<tr>
		<td>geoData</td>
		<td>User location information</td>
		<td>String</td>
		<td>If this information is available, please, notify administration for finish format agreement</td>
		<td>No</td>
		<td>No</td>
	</tr>
	<tr>
		<td>countryCode</td>
		<td>Country code</td>
		<td>String</td>
		<td>ISO-3166-1 (2 latin letters)</td>
		<td>No</td>
		<td>No</td>
	</tr>
	<tr>
		<td>bannerAmount</td>
		<td>Number of banners to show</td>
		<td>Integer</td>
		<td>[1-3]</td>
		<td>No</td>
		<td>No</td>
	</tr>
</table>

Only for *html* response format there is non required additional parameter **markup** - markup type (2 - wml, 3 - xhtml, default is 3).

#### 4.2.2 Additional headers parameters

Every user headers that begins with "accept-" и "x-" and also "host", "referer", "via" must be transmitted with prefix "x-plus1-".

Additionally, header "x-plus1-remote-addr" with client IP address, and header "x-plus1-user-agent" with browser's user-agent should be transmitted.

### 4.3 User uniqueness

There is two ways to provide user uniqueness:

* login parameter

* cookie with name "wssid".

In second case you must set cookies by yourself.
In xml response format there is a node with name "cookieSetterUrl". In it there is image, that user must download(show it in html should be enough).
In html response format there is a tag with this image by default.

##5. Response

![Parsing API response](https://github.com/WapStart/plus1-connect/raw/master/doc/en-api-response.jpg)

##6. Request and response examples

###6.1 Request to direct controller with xml response format

Request:

    GET /direct/xml/id/8/?sex=1&age=42&kws=russia%2Cmobile%20adv%2Cwapstart&pageId=01234&location=42;43 HTTP/1.1
    Host: ro.plus1.wapstart.ru
    User-agent: Android, linux, htc hero
    Cookies: wssid=e1f9c9ebab3014bc65940ab812c8629bf25dd86e
    x-display-metrics: 480x320
    \r\n\r\n

Response:

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


In this case ad network server finds banner, which suites the request, in xml format containing fields:

* *id* - banner identifier in Plus1 ad network (just for reference)

* *title* - banner title

* *content* - banner sub-title

* *responseType* - type of interaction (currently available only one type – “1”, which means "to follow a link")

* *link* - URL, that needs to open in a standard browser upon clicking on banner

* *pictureUrl* - link to the actual banner (creative)

* *pictureUrlPng* - link to banner in png format

###6.2 Request to proxy controller with html response format

Request:

    GET /proxy/html/id/2562/?pageId=7bf4a3975516ea84f4c1c5893019d581b227e731
    &ip=83.149.32.132&encoding=1&sex=2&age=36&kws= russia%2Cmobile%20adv%2Cwapstart
    &geoData=a%3A2%3A%7Bs%3A2%3A%22id%22%3Bs%3A4%3A%222562%22%3Bs%3A7%3A%22geoData%22%3Bs%3A4%3A%225393%22%3B%7D HTTP/1.1
    Host: ro.plus1.wapstart.ru
    User-Agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
    x-plus1-user-agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
    x-plus1-remote-addr: 83.149.32.132

Response:

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

###6.3 Request to proxy controller with xml response format

Request:

    GET proxy/html/id/2562/?pageId=7bf4a3975516ea84f4c1c5893019d581b227e731
    &ip=83.149.32.132&encoding=1&sex=2&age=36&kws=russia%2Cmobile%20adv%2Cwapstart
    &geoData=a%3A2%3A%7Bs%3A2%3A%22id%22%3Bs%3A4%3A%222562%22%3Bs%3A7%3A%22geoData%22%3Bs%3A4%3A%225393%22%3B%7D HTTP/1.1
    Host: ro.plus1.wapstart.ru
    User-Agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
    x-plus1-user-agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
    x-plus1-remote-addr: 83.149.32.132
    \r\n\r\n

Response:

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

In this case ad network server finds banner, which suites the request, in xml format containing fields:

* *id* - banner identifier in Plus1 ad network (just for reference)

* *title* - banner title

* *content* - banner sub-title

* *link* - URI, e.g. URL of the landing page

* *pictureUrl* - the link to the banner image itself

* *cookieSetterUrl* – picture that setting a cookie to end-user

* *singleLineContent* – text banner as one row (for text ads only)
