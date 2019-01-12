---
title: javascript实现Haversine公式计算两个经纬度坐标点距离
date: 2018-03-06 15:47:24
categories: 
  - Chart&DataVisualization
  - Leaflet
tags: 
  - Haversine 
  - JavaScript 
  - leaflet
---

### javascript实现Haversine公式计算两坐标点距离

```code
//javascript实现Haversine公式

/**
* 角度转弧度
* @param {Number} degree
*/
function toRadians(degree) {
    return degree * Math.PI / 180;
}
```

...
<!-- more -->

```code

/**
* 计算两点之间距离
* @param {*} latitude1
* @param {*} longitude1
* @param {*} latitude2
* @param {*} longitude2
*/
function distance(latitude1, longitude1, latitude2, longitude2) {
    // R是地球半径，以㎞为单位
    var R = 6371;

    var deltaLatitude = toRadians(latitude2-latitude1);
    var deltaLongitude = toRadians(longitude2-longitude1);
    latitude1 = toRadians(latitude1);
    latitude2 = toRadians(latitude2);

    var a = Math.sin(deltaLatitude/2) *
            Math.sin(deltaLongitude/2) +
            Math.cos(latitude1) *
            Math.cos(latitude2) *
            Math.sin(deltaLongitude/2) *
            Math.sin(deltaLongitude/2);

    var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
    var d = R * c;

    return d;
}

```

参考: http://www.cocoachina.com/ios/20141118/10238.html