---
title: Cesium基础操作
date: 2016-11-03 11:35:58
categories: 
  - Chart&DataVisualization
tags:
  - Cesium
---

<style>
table{
  border:0;margin:0;border-collapse:collapse;border-spacing:0;
  font-size: 13px;
}
</style>
### 1. 初始化viewer对象

```code
  //创建cesium Viewer
  viewer = new Cesium.Viewer(‘cesiumContainer’,{
    animation:false, //是否创建动画小器件，左下角仪表
    baseLayerPicker:false, //是否显示图层选择器
    fullscreenButton:false, //是否显示全屏按钮
    geocoder:false, //是否显示geocoder小器件，右上角查询按钮
    homeButton:false, //是否显示Home按钮
    infoBox : false, //是否显示信息框
    sceneModePicker:false, //是否显示3D/2D选择器
    selectionIndicator : false , //是否显示选取指示器组件
    timeline:false, //是否显示时间轴
    navigationHelpButton:false, //是否显示右上角的帮助按钮
    scene3DOnly : true, //如果设置为true，则所有几何图形以3D模式绘制以节约GPU资源
    navigationInstructionsInitiallyVisible:false,
    showRenderLoopErrors:false,
    imageryProvider : new Cesium.OpenStreetMapImageryProvider({ url : '//a.tile.openstreetmap.org/' }) //加载自定义地图瓦片需要指定一个自定义图片服务器//URL 为瓦片数据服务器地址
  });
```

<!-- more -->

### 2. 对entity的操作：添加、隐藏、修改、去除、居中显示

```code
  Var rainEntity=viewer.entities.add({
      id: "rain",
    name: 'RainStation',
    parent: rainLayer3D,
    position: Cesium.Cartesian3.fromDegrees(lon, lat),
    billboard: {
    image: 'images/pointIcons/rain1.png',
    scale:0.7,
    verticalOrigin: Cesium.VerticalOrigin.BOTTOM
  },
  label: {
    text: rainfall,
    font: '12px SimHei ',
    Width: 3,
    style: Cesium.LabelStyle.FILL,
    fillColor: Cesium.Color.AQUA,
    horizontalOrigin: Cesium.HorizontalOrigin.CENTER,
    verticalOrigin: Cesium.VerticalOrigin.TOP
  }
    });  //添加
  viewer.entities.getById("rain").show = false;   //隐藏
  viewer.entities.getById("rain").label.text= "drp";   //修改属性
  viewer.entities.removeAll();  //移除所有
  viewer.zoomTo(rainEntity);   //居中显示
```

### 3. 去掉entity的双击事件

  问题所在：双击entity，会放大视图，entity居中显示，且鼠标左键失去移动功能，鼠标滚轮失去作用  
  解决问题：

```code
  viewer.screenSpaceEventHandler.setInputAction(function(){},Cesium.ScreenSpaceEventType.LEFT_DOUBLE_CLICK );
```

### 4. 获取当前视角高度

```code
  var scene = viewer.scene;
  var ellipsoid = scene.globe.ellipsoid;
  var height=ellipsoid.cartesianToCartographic(viewer.camera.position).height;
```

### 5. 获取某个经纬度在屏幕上的位置

```code
  var position = Cesium.Cartesian3.fromDegrees(lon, lat);
  var clickPt =Cesium.SceneTransforms.wgs84ToWindowCoordinates (viewer.scene, position);
  var screenX=clickPt.x;
  var screenY=clickPt.y;
```

### 6. 获取三维场景屏幕中心点坐标

```code
  var result = viewer.camera.pickEllipsoid(new Cesium.Cartesian2 ( viewer.canvas.clientWidth /2 , viewer.canvas.clientHeight / 2));
  var curPosition = Cesium.Ellipsoid.WGS84.cartesianToCartographic(result);
  var lon = curPosition.longitude*180/Math.PI;
  var lat = curPosition.latitude*180/Math.PI;
```

### 7. 响应鼠标单击等事件，获取屏幕点击坐标

```code
  var handler = new Cesium.ScreenSpaceEventHandler(canvas);
  handler.setInputAction(function(click){},Cesium.ScreenSpaceEventType.LEFT_CLICK);
  var clickX=click.position.x;
  var clickY=click.position.y;
```

这个LEFT_CLICK可以换成MIDDLE_CLICK、MOUSE_MOVE等就会响应滚轮点击、鼠标移动等事件，见参考文档中的ScreenSpaceEventType()，注意不同的事件中，function中的click会有不同的属性，可console.log(click)，找到所需

### 8. 跟踪相机视角的改变

```code
    viewer.camera.moveStart.addEventListener(function(moveStartPosition){})；
    viewer.camera.moveEnd.addEventListener(function(moveEndPosition){})；
```

其实还有个
viewer.camera.changed.addEventListener(function(moveEndPosition){})，但我不会用，总是提示changed不存在，但是camera的参考文档中这个changed和moveStart和moveEnd都可以addEventListener

### 9. 使视角到达某一地点

```code
    viewer.camera.setView({
        destination: Cesium.Cartesian3.fromDegrees(lon, lat, height),
        orientation: {
            heading : curHeading,  //左右偏移
            pitch : curPitch,   //上下偏移
            roll : 0.0                           
        }
    });
```

### 10. 根据鼠标点击，修改camera位置和方向

```code
    new Cesium.ScreenSpaceCameraController(scene)
```

### 11. 时间轴--时间轴是用于显示和控制当前场景时间的部件

```code
    new Cesium.Timeline(container, clock)
```

| 名称	   | 类型       | 描述       |
| -------- |:----------:|-------------------|
| container| Element	| 时间线组件的父元素s |
| clock	   | [Clock](http://cesiumjs.org/Cesium/Build/Documentation/Clock.html)	    | 要使用的clock      |

属性：container : Element 父容器

方法：

>destroy()	销毁窗口小部件。 如果从布局中永久删除窗口小部件，应该调用。
>isDestroyed() → Boolean   返回：如果对象已被销毁，则为true，否则为false。
>resize()   调整窗口小部件的大小以匹配容器大小。
>zoomTo(startTime, stopTime)将视图设置为所提供的时间。
>
>|名称	|类型	|描述|
|-------|-------|----------|
|startTime	|[JulianDate](http://cesiumjs.org/Cesium/Build/Documentation/JulianDate.html)	|开始时间|
|stopTime	|[JulianDate](http://cesiumjs.org/Cesium/Build/Documentation/JulianDate.html)	|结束时间|

12. Clock

```code
    new Cesium.Clock(options)
```

>成员：
>
> |名称	 |类型	|默认值	|描述|
    |---|----|----|------|
    |startTime	|[JulianDate](http://cesiumjs.org/Cesium/Build/Documentation/JulianDate.html)	|	|可选  时钟的开始时间.
    |stopTime	|[JulianDate](http://cesiumjs.org/Cesium/Build/Documentation/JulianDate.html)	|	|可选 时钟的停止时间。
    |currentTime	|[JulianDate](http://cesiumjs.org/Cesium/Build/Documentation/JulianDate.html)|		|可选 当前时间
    |multiplier	|Number	|1.0	|可选  确定在调用Clock＃tick时前进的时间，允许向后推进的负值。
    |clockStep	|[ClockStep](http://cesiumjs.org/Cesium/Build/Documentation/ClockStep.html)	|ClockStep.SYSTEM_CLOCK_MULTIPLIER	|可选  确定调用Clock＃tick是否依赖于帧或系统时钟。
    |clockRange	|[ClockRange](http://cesiumjs.org/Cesium/Build/Documentation/ClockRange.html)	|ClockRange.UNBOUNDED	|可选   确定当达到Clock＃startTime或Clock＃stopTime时时钟应如何工作。
    |canAnimate	|Boolean	|true	|可选   指示Clock＃tick是否可以提前时间。 例如，如果正在缓冲数据，这可能是假的。 时钟将只有当Clock＃canAnimate和Clock＃shouldAnimate都为真时才会计时。
    |shouldAnimate	|Boolean	|true	|可选  指示Clock＃tick是否应尝试提前时间。 时钟将只有当Clock＃canAnimate和Clock＃shouldAnimate都为真时才会计时。

  >方法:  
> tick() → [JulianDate](http://cesiumjs.org/Cesium/Build/Documentation/JulianDate.html)  
> 根据当前配置选项从当前时间提前时钟。 tick应该被称为每一帧，而不管动画是否发生。 要控制动画，请使用Clock＃shouldAnimate属性。
	返回：
  Clock＃currentTime属性的新值  
>首先，Viewer在初始化时，内部会创建一个Clock，所以建议用户使用viewer.cesiumWidget.clock而不是自己创建Clock，毕竟在一个应用内，时间通常都是标准的，创建多个Clock反而混淆了。  
>Clock中默认开始时间（startTime）为当前时间，终止时间（stopTime）为24小时后，并能获取当前时间（currentTime）。
另外可以设置ClockRange属性，用户可以根据自己的需要来设置，默认为: UNBOUNDED
>>CLAMPED-----达到终止时间后停止  
>>LOOP_STOP----达到终止时间后重新循环
>>UNBOUNDED----达到终止时间后继续读秒
>JulianDate
> Clock内部以儒略日（JulianDate）维护时间。其起始日期为公元前4713年1月1日中午12时，这和我们常用的格林威治时间略有不同，主要是天文学家使用。
>
>JulianDate类提供了非常丰富的接口，时间的对比，运算，和格林威治时间的转换等，简单易用，完全满足各类需求。同时内部还可以采用国际原子时(TAI)的方式来记录。下面是Clock的一个简单用法：  
>![http://i.imgur.com/Zd8rcBG.png](http://i.imgur.com/Zd8rcBG.png)  
>最后要强调的是tick方法，Cesium内部每一帧都会调用该方法，实现时间状态的更新和检测。涉及到时间的细节很多，比如TimeInterval，TimeConstants

### 13. JulianDate

```code
    Cesium.JulianDate.toDate(JulianDate)--将JulianDate转成js的Date对象

    eg：Cesium.JulianDate.toDate(viewer.cesiumWidget.clock.currentTime)
```