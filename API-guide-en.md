API - Plus1 Wapstart implementation guide
=========================================

1. Introduction
---------------

This document provides Plus1 API implementation guide for mobile sites and applications.
A site needs to generate http GET call, if they wish to host Plus1 ads. In return, it receives either a banner (in one of the available formats) or a backup ad.

Note: You can also use our ready set of tools for accessing Plus1 API. They are PHP/Perl executive codes, Java Script client and SDK for iOs/Android/Java. These tools are available in your account settings at plus1.wapstart.ru.

2. Request URL
--------------
Request URL goes through host: http://ro.plus1.wapstart.ru

*We recommend using Name, not IP*

3. Banner ad specifications
---------------------------
We work with text, banner and combined format (text + banner) ads, using MMA (pdf) format. There is always a clickable up to 35 symbols long header in text ads, additional text of 35 symbols can also be used. Graphic banners are in GIF format (including animated GIF), JPG, PNG.

4. Controllers and request parameters
-------------------------------------

Controllers that handle a request are divided into two groups:
- application site
- ad network server (viewBanner and viewBannerXml).

Parameters can be passed through:
- call query_string
- additional call headers

Depending on the choice of controllers, passed parameters will differ.

5. Requests from APPs
---------------------

When making request from App the controller should be as follows:
/?area=application&version=2, where version = 2 stands for API version

Query_string should contain the following parameters:
* *id* – site ID in Plus1. Unsigned integer. Required.
* *pageId* - ad-cast unique identifier, row [0-9a-f] {40} Within one pageId the rotator will try to find different banners. To show several different banners on one screen you should make requests with the same pageId. Required.
* *encoding* – code identifier. 1 for utf-8 and 2 for cp1251. Optional. By default 1.
* *sex* - user gender. 1 for male and 2 for female. Optional.
* *age* – user age. Optional.
* *kws* – user keywords. URL encoded string containing comma separated values. Optional.
* *login* - login, or any other user identifier. In order to keep this confidential we recommend using its hash function rather than login itself. Optional.
* *location* – geographical location in WGS-84 format. Longitude and latitude separated by semi-colon. For example, location=37.677751;55.757718 Optional.
* *realSiteId* - parameter for partner networks - site ID in your network. Unsigned integer. Optional.

All parameters characterizing device (brand/model/OS) must be conveyed by means of additional headers.

Required parameters are:

* *User-Agent* - regular device user agent
* *Cookies* - cookies that Plus1 server assigned to user with first request
* *Host: ro.plus1.wapstart.ru* - HTTP/1.1 specification

Other desirable parameters:
* *x-display-metrics* – display metrics in "1024x768" format.
* *x-application-type* – application paltform. Arbitrary string.
* *x-device-imei* - imei device imei. Unsigned integer.

Also can be passed:
*x-preferred-locale* - user locale. String.

Additional parameters (in order to improve targeting) can be passed upon Plus1 admin approval.

5.1 Parsing response
--------------------
Here is a brief scheme of parsing response from API:

![Parsing API response](https://github.com/WapStart/plus1-connect/raw/master/doc/en-api-response.jpg)

Example request to the controller application would look as follows:

	GET /?area=application&version=2&id=8&sex=1&age=42&kws= russia%2Cmobile%20adv%2Cwapstart&pageId=0000000000111111111122222222224444444444&location=42;43 HTTP/1.1
    Host: ro.plus1.wapstart.ru
    User-agent: Android, linux, htc hero
    Cookies: wssid=e1f9c9ebab3014bc65940ab812c8629bf25dd86e
    x-display-metrics: 480x320
    \r\n\r\n

Example response:


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

In this case, our server has found a banner and returned xml response, containing the following fields:

* *id* – banner ID in plus1 (reference)
* *title* – banner title
* *content* – banner text
* *responseType* - type of interaction. Currently available only one type – “1”, which means "to follow a link"
* *link* - URL, that needs to open in a standard browser upon clicking on banner
* *pictureUrl* - link to the actual banner (creative)
* *pictureUrlPng* – link to banner in png format

6. Request from server (web site or ad network)
-----------------------------------------------

There are currently two controllers used for API:
* viewBanner – response with html block with banner
* viewBannerXml – response with xml format banner

When calling API host controller should look as follows:

/?area=viewBanner&version=2 или /?area=viewBannerXml&version=2 *where version = 2 stands for using the second version of API

Note: it is recommended to get acquainted with Plus1BannerAsker.class.php, that utilizes the described below logic in php language.

Required parameters in query_string:

* *id* – site ID in Plus1. Unsigned integer. Required.
* *pageId* - ad-cast unique identifier, row [0-9a-f] {40} Within one pageId the rotator will try to find different banners. To show several different banners on one screen you should make requests with the same pageId. Required.
* *ip* - client’s ip4 address. Required.
* *encoding* – code identifier. 1 for utf-8 and 2 for cp1251. Optional. By default 1.
* *sex* - user gender. 1 for male and 2 for female. Optional.
* *age* – user age. Optional.
* *kws* – user keywords. URL encoded string containing comma separated values. Optional.
* *login* - login, or any other user identifier. In order to keep this confidential we recommend using its hash function rather than login itself. Optional.
* *geoData* – geo information string (city, state, country divided by “;”). Final format to be approved by admin. Optional.
* *countryCode* – country, where user is located in ISO-3166-1 format (2 Latin letters). Optional.
* *markup* - type of markup (use only with controller viewBanner). 3 is for xhtml and 2 is for wml. 3 by default. Optional.
* *bannerAmount* – number of banners. Unsigned integer from 1 to 3. Optional. By default 1.

Parameters that must be translated in headers:

All mobile device headers containing "accept-", "x-", "host", "referer", "via" should be passed with API request. Please prefix all headers with “x-plus1-”
Additionally you need to pass as a header "x-plus1-remote-addr" the value of ip address (from remoteaddr). Additionally, header "x-plus1-remote-addr" should be passed onto rotator, by means of adding it into clients IP address.
User-Agent must be passed twice, as the string "User-Agent" itself and as the value of header "x-plus1-user-agent".

Providing a unique definition of user:

If possible, please provide us with information about unique users. You can use two mechanisms:
-  login parameter
-  transfer "cookie" named wssid to our server. You have to provide the set-up of this cookie on your own.
XML containing banner (controller viewBannerXml) has a node named cookieSetterUrl. This node contains a pixel that needs to be loaded by the user. HTML banner contain this pixel by default.

6.1 Brief description of parsing response process:
--------------------------------------------------

Example request to the controller viewBanner would be as follows:

	GET /?area=viewBanner&version=2&id=2562&pageId=7bf4a3975516ea84f4c1c5893019d581b227e731
	&ip=83.149.32.132&encoding=1&sex=2&age=36&kws= russia%2Cmobile%20adv%2Cwapstart
	&geoData=a%3A2%3A%7Bs%3A2%3A%22id%22%3Bs%3A4%3A%222562%22%3Bs%3A7%3A%22geoData%22%3Bs%3A4%3A%225393%22%3B%7D HTTP/1.1
	Host: ro.plus1.wapstart.ru
	User-Agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
	x-plus1-user-agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
	x-plus1-remote-addr: 83.149.32.132


And an example response:

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


Example request to the controller viewBannerXml would be as follows:

	GET
	/?area=viewBannerXml&version=2&id=2562&pageId=7bf4a3975516ea84f4c1c5893019d581b227e731
	&ip=83.149.32.132&encoding=1&sex=2&age=36&kws= russia%2Cmobile%20adv%2Cwapstart
	&geoData=a%3A2%3A%7Bs%3A2%3A%22id%22%3Bs%3A4%3A%222562%22%3Bs%3A7%3A%22geoData%22%3Bs%3A4%3A%225393%22%3B%7D HTTP/1.1
	Host: ro.plus1.wapstart.ru
	User-Agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
	x-plus1-user-agent: Nokia3510i/1.0 (05.25) Profile/MIDP-1.0 Configuration/CLDC-1.0,86
	x-plus1-remote-addr: 83.149.32.132
	\r\n\r\n


And an example response:

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

In this case, our server has found a banner and returned in the response xml, containing the following fields:
* *id* - banner ID (reference)
* *title* - banner title
* *content* - banner sub-title
* *link* - URI, e.g. URL of the landing page
* *pictureUrl* - the link to the banner image itself
* *cookieSetterUrl* – picture that setting a cookie to end-user
* *singleLineContent* – text banner as one row (for text ads only)
