---
title: 浏览器navigator对象
date: 2017-01-01
categraies: 
	- JavaScript
tags: 
	- javascript
---

# navigator

Navigator 对象包含有关浏览器的信息。

注释：没有应用于 navigator 对象的公开标准，不过所有浏览器都支持该对象。
###Navigator 对象集合###
>集合：plugins[]
>
>描述：返回对文档中所有嵌入式对象的引用。
该集合是一个 Plugin 对象的数组，其中的元素代表浏览器已经安装的插件。Plug-in 对象提供的是有关插件的信息，其中包括它所支持的 MIME 类型的列表。
虽然 plugins[] 数组是由 IE 4 定义的，但是在 IE 4 中它却总是空的，因为 IE 4 不支持插件和 Plugin 对象。
<!--more-->
###Navigator 对象属性###

>appCodeName	 ------------------返回浏览器的代码名。
>
>appMinorVersion----------------	返回浏览器的次级版本。
>
>appName-------------------------	返回浏览器的名称。
>
>appVersion-----------------------	返回浏览器的平台和版本信息。
>
>browserLanguage---------------	返回当前浏览器的语言。
>
>cookieEnabled-------------------	返回指明浏览器中是否启用 cookie 的布尔值。
>
>cpuClass---------------------------返回浏览器系统的 CPU 等级。
>
>onLine------------------------------返回指明系统是否处于脱机模式的布尔值。
>
>platform-----------------------------返回运行浏览器的操作系统平台。
>
>systemLanguage-----------------返回 OS 使用的默认语言。
>
>userAgent--------------------------返回由客户机发送服务器的 user-agent 头部的值。
>
>userLanguage---------------------返回 OS 的自然语言设置。

###Navigator 对象方法###

>javaEnabled()----------------------规定浏览器是否启用 Java。
>
taintEnabled()----------------------规定浏览器是否启用数据污点 (data tainting)。

----------

在新的API标准中，可以通过navigator.geolocation来获取设备的当前位置，返回一个位置对象，用户可以从这个对象中得到一些经纬度的相关信息。

navigator.geolocation的三个方法：

>1. getCurrentPosition()
>2. watchPosition()
>3. clearWatch()

***getCurrentPosition()***

使用方法：navigator.geolocation.getCurrentPosition(successCallback, [errorCallback] , [positionOptions]);

A) successCallback 获取定位成功时执行的回调函数 eg: function(position){alert("纬度："+position.coords.latitude+"；经度："+position.coords.longitude)};

successCallback返回一个地理数据对象position作为参数，该对象有属性timestamp和coords。timestamp表示该地理数据创建时间（时间戳）；coords包括另外七个属性：

>1. coords.latitude：估计纬度
2. coords.longitude：估计经度
3. coords.altitude：估计高度
4. coords.accuracy：所提供的以米为单位的经度和纬度估计的精确度
5. coords.altitudeAccuracy：所提供的以米为单位的高度估计的精确度
6. coords.heading： 宿主设备当前移动的角度方向，相对于正北方向顺时针计算
7. coords.speed：以米每秒为单位的设备的当前对地速度

PS：firefox下还有address属性，可以获取详细地址，不过我得到的地址是错误的，使用方法：position.address.city，具体如下：
	
	QueryInterface：function QueryInterface() { [native code] }
	streetNumber：200号
	street：人民大道
	premises：null
	city：上海市
	county：null
	region：上海市
	country：中国
	countryCode：CN
	postalCode：null
	getInterfaces：function getInterfaces() { [native code] }
	getHelperForLanguage：function getHelperForLanguage() { [native code] }
	contractID：null
	classDescription：null
	classID：null
	implementationLanguage：2
	flags：8
	SINGLETON：1
	THREADSAFE：2
	MAIN_THREAD_ONLY：4
	DOM_OBJECT：8
	PLUGIN_OBJECT：16
	CONTENT_NODE：64
	RESERVED：2147483648

address属性

B) errorCallback 定位失败时执行的回调函数 eg: function(error){alert(error.message);}

errorCallback返回一个错误数据对象error作为参数，该对象有属性：

　　1.code :表示失败原因，返回1 or 2 or 3 ，具体为

　　　　PERMISSION_DENIED (数值为1) 表示没有权限使用地理定位API

　　　　POSITION_UNAVAILABLE (数值为2) 表示无法确定设备的位置，例如一个或多个的用于定位采集程序报告了一个内部错误导致了全部过程的失败

　　　　TIMEOUT (数值为3) 表示超时

　　　　详情查看 http://dev.w3.org/geo/api/spec-source.html#permission_denied_error

　　2.message :错误提示内容 

C) positionOptions 用来设置positionOptions来更精细的执行定位，positionOptions拥有三个属性{enableHighAccuracy:boolean , timeout:long , maximumAge:long}。

>enableHighAccuracy 【true or false(默认)】是否返回更详细更准确的结构，默认为false不启用，选择true则启用，但是会导致较长的响应时间及增加功耗，这种情况更多的用在移动设备上。

>timeout 设备位置获取操作的超时时间设定（不包括获取用户权限时间），单位为毫秒，如果在设定的timeout时间内未能获取位置定位，则会执行errorCallback()返回code（3）。如果未设定timeout，那么timeout默认为无穷大，如果timeout为负数，则默认timeout为0。

>maximumAge 设定位置缓存时间，以毫秒为单位，如果不设置该值，该值默认为0，如果设定负数，则默认为0。该值为0时，位置定位时会重新获取一个新的位置对象；该值大于0时，即从上一次获取位置时开始，缓存位置对象，如果再次获取位置时间不超过maximumAge，则返回缓存中的位置，如果超出maximumAge，则重新获取一个新的位置。

***watchPosition()***

功能getCurrentPosition()相似，watchPosition()是定期轮询设备的位置，同样拥有3个参数，与getCurrentPosition()相同。

>使用方法：navigator.geolocation.watchPosition(successCallback, [errorCallback] , [positionOptions]);

>执行步骤：

>>1.首先初始化positionOptions内的属性（详细同上）。

>>2.判断是否有缓存位置对象，该对象年龄是否可用、是否超出maximumAge ，如果可用且未超出，返回缓存位置，否则重新确定设备位置。

>>3.位置定位操作：

>>>i.建立一个计时器，进行位置获取操作，如果在timeout之前完成，执行下一步；如果未在timeout之前完成，则执行errorCallback()，code为3，跳出步骤做等待重新激活。

>>>ii.如果在timeout之前获得位置成功，则执行successCallback()，然后重置计时器（从获取位置成功时刻重新算起），继续挂起获取新位置。当有与之前位置有明显不同位置出现时，再次执行successCallback()，并重复操作，该循环直到timeout超时或者获取操作中遇到POSITION_UNAVAILABLE错误执行errorCallback()为止，亦或者执行clearWatch()操作。

***clearWatch()***

>配合watchPosition()使用，用于停止watchPosition()轮询。
	
>watchPosition()需要定义一个watchID，var watchID = watchPosition(...)，通过clearWatch(watchID)来停止watchPosition()，使用方法类似setInterval。