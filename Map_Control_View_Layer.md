# Map类
---
Map Openlayers3的主体构成。  
* layers  
* view  
* raeget  



      var map = new ol.Map({  
        target: 'map',  
        layers: [  
          new ol.layer.Tile({  
            source: new ol.source.MapQuest({layer: 'sat'})  
          })  
        ],  
        view: new ol.View({  
          center: ol.proj.transform([37.41, 8.82], 'EPSG:4326', 'EPSG:3857'),  
          zoom: 4  
        })  
      });

   
target 添加一个div
layers 创建不同的添加到这个数组中
view   承载地图的框框  

根据API我们可以看到实际上创建一个Map的参数远远不止这三个  

* controls 地图控件
* pixelRatio 像素比例 参数为数组或者windows.devivePixelRation
* interactions 相互作用与地图的互动
* layers 图层
* loadTilesWholeAnimating 默认false 加载瓦片图层的动画
* loadTilesWhileInteracting
* logo
* overlays 可以认为是默认的一个图层
* renderer 渲染
* target 容器 div
* view 视图  

---

# control 类

      OpenLayers 中的控件是由 control 类定义的，这是一个虚基类，不负责实例化特定的控件，它的主要作用是让其他具体的种类的控件类实现继承。OpenLayers 中包含的控件有：

* controldefaults，地图默认包含的控件，包含缩放控件和旋转控件；
* fullscreencontrol，全屏控件，用于全屏幕查看地图；
* mousepositioncontrol，鼠标位置控件，显示鼠标所在地图位置的坐标，可以自定义投影；
* overviewmapcontrol，地图全局视图控件；
* rotatecontrol，地图旋转控件；
* scalelinecontrol，比例尺控件；
* zoomcontrol，缩放控件；
* zoomslidercontrol，缩放刻度控件；
* zoomtoextentcontrol，缩放到全局控件。

 [详细参见庆祝亚运会博客](http://blog.csdn.net/qingyafan/article/details/45715925)

---
# View类


* rotate(rotation , opt_anchor )，接受两个参数，旋转角度数（rotation）和旋转中心（opt_anchor，可选），将地图围绕 opt_anchor 旋转 rotation 角度；

* isDef，检查地图的中心和分辨率是否已经设置，都设置返回 true，否则返回 false；

* fitExtent(extent, size)，接受两个参数：extent 和 size，extent 类型是 ol.Extent – [left, bottom, right, top]，size由map.getSize()获取；该功能类似于 ArcGIS 的缩放到图层功能，将地图的view 缩放到 extent 区域可见的合适尺度；这个方法注意坐标转换。
<pre><code>
view.fitExtent(ol.proj.transformExtent([76,18,140,56], 'EPSG:4326', 'EPSG:3857'), map.getSize());
其中 [76,18,140,56]是中国的外包矩形坐标，结果应该是适合显示中国全图的尺度。
</code>
</pre>


* fitGeometry(geometry, size, opt_options)，参数是地理要素，地图尺寸和可选参数；根据给定的地理要素，将 view 缩放到适合地理要素显示的尺寸；

* get ，getCenter() 获取地图的中心，返回一个地图中心的坐标；getResolution()获取地图的分辨率，即比例尺，返回一个表示比例尺的数值； 
* 
* getProjection()获取地图使用的”投影坐标系统”，如EPSG:4326；getRotation()获取地图的“旋转角度”；getZoom()获取地图的缩放级别，返回一个表示缩放级别的数值；以上的函数均不需要任何参数；

* set ，setCenter(center)，参数为ol.Coordinate类型 – [x, y]，作用为设置地图的中心坐标；

* setResolution(number)设置地图的分辨率（比例尺）；setRotation(number)，参数为旋转的角度对应的值，并不是度数，如Math.PI，不是180度，作用是将地图旋转相应点角度，顺时针为正；* * setZoom(number)设置地图的缩放级别。

    这些方法中，fit类方法和 set 类方法，调用都可以看到效果，其它的都是计算或者获取相应已经存在的值。下面我们通过实例来看看这些方法的效果。

  [详细参见庆祝亚运会博客](http://blog.csdn.net/qingyafan/article/details/45603415)



---

# Layers 类

* 参数

* brightness，亮度，默认为 0
* contrast，对比度，默认为 1
* hue，色调，默认为0 ；
* opacity，透明度，默认为 1 ，即完全透明；
* saturation，饱和度，默认为 1 ；
* source，图层的来源，如果在构造函数中没有传入相应的参数，可以调用 ol.layer.Layer#setSource方法来设置来源： layer.setSource(source) ；
* visible，是否可见，默认为 true ；
* extent，图层渲染的区域，即浏览器窗口中可见的地图区域。extent 是一个矩形范围，格式是[number, number, number, number] 分别代表 [left, bottom, right, top] 。如果没有设置该参数，图层就不会显示；
* minResolution，图层可见的最小分辨率，当图层的缩放级别小于这个分辨率时，图层就会隐藏；
* maxResolution，图层可见的最大分辨率，当图层的缩放级别等于或超过这个分辨率时图层就会隐藏。  

---
* 方法： 
- getLayersArray( )，得到所有图层组成的数组； 
- getLayerStatesArray( )，得到所有图层状态组成的数组； 
- getSource( )，得到相应图层的来源； 
- getSourceState( )，得到相应图层的来源状态； 
- handleSourceChange_( )，处理 source 变动的函数； 
- handleSourcePropertyChange_( )，处理 source 属性变动的函数； 
- setSource( )，设置图层 source 属性，参数为一个 source 对象。

---
* 私有方法： 
- visibleAtResolution( )，参数是 layerState 和 resolution，如果图层可见，返回 true ，如果传入了 resolution，也会比较 resolution 是否在 minResolution 和 maxResolution 之间。

 [参考庆祝闫亚运会博客](http://blog.csdn.net/qingyafan/article/details/45398131)


---

* 实例化各类图层


* 热力图
```
ol.layer.HeatmapLayerProperty = {
  BLUR: 'blur',//边缘模糊
  GRADIENT: 'gradient',//半径
  RADIUS: 'radius'
};
```
创建heatmaplayer对象：
```
var vector = new ol.layer.Heatmap({
  source: new ol.source.KML({
    url: 'data/kml/2012_Earthquakes_Mag5.kml',
    projection: 'EPSG:3857',
    extractStyles: false
  }),
  blur: parseInt(blur.value, 10),
  radius: parseInt(radius.value, 10)
});
```
这里 heatmap 使用KML格式，本地文件data/kml/2012_Earthquakes_Mag5.kml 作为 heatmap 的来源，数据是2012年全球地震发生的位置和震级等简单的描述信息，然后将 heatmap 图层加到 map 中：
```
map = new ol.Map({  //初始化map
    target: 'map',
    layers: [
      new ol.layer.Tile({
        source: new ol.source.MapQuest({layer: 'sat'})
      }),
      heatmap
    ],
    view: new ol.View({
      center: ol.proj.transform([37.41, 8.82], 'EPSG:4326', 'EPSG:3857'),
      zoom: 4
    })
});
```
---
* ImageLayer 图片图层
首先实例化一幅图片图层：
```
/**
 * create an imageLayer 
 */
var extent = [0, 0, 3264, 2448];
var projection = new ol.proj.Projection({
            code: 'EPSG:4326',
            extent: extent
        }),
var imageLayer = new ol.layer.Image({
    source: new ol.source.ImageStatic({
        url: 'sample.jpg',
        projection: projection,
        imageExtent: extent//4角坐标集
    })
})
```
        与 heatmap 一样，首先需要传入 URL 参数，即图片地址，这里可以是网络图片的地址，或者是本地的文件地址；然后需要传入参考坐标系 projection，code 是一个标识，可以是任何字符串，如果是EPSG：4326 或者是 EPSG：3857 ，那么就会使用这两个坐标系，如果不是，就使用默认的坐标系，extent 是一个矩形范围，上面已经提到；imageLayer 的第三个参数是 imageExtent 表示图片的尺寸，这里我们不能改变图片的原来的比例，图片只会根据原来的比例放大或缩小。

* 最后将 imageLayer 加到地图中：
```
map = new ol.Map({  //初始化map
    target: 'map',
    layers: [ imageLayer ],
    view: new ol.View({
      projection: projection,
      center: ol.extent.getCenter(extent),
      zoom: 2
    })
}); 
```

---

* TileLayer 瓦片图层
```
map = new ol.Map({  //初始化map
    target: 'map',
    layers: [
      new ol.layer.Tile({
        source: new ol.source.MapQuest({layer: 'sat'})
      })
    ],
    view: new ol.View({
      center: ol.proj.transform([37.41, 8.82], 'EPSG:4326', 'EPSG:3857'),
      zoom: 2
    })
}); 
```
这个图层有个投机取巧的方法，就是发布预览图直接复制~~！

---

* vectorlayer
```
vectorLayer = new ol.layer.Vector({ //初始化矢量图层
  source: new ol.source.GeoJSON({
    projection: 'EPSG:3857',
    url: 'data/geojson/countries.geojson'   //从文件加载边界等地理信息
  }),
  style: function(feature, resolution) {
    style.getText().setText(resolution < 5000 ? feature.get('name') : '');  //当放大到1:5000分辨率时，显示国家名字
    return [style];
  }
});
```
* 定义样式
```
/**
 * 定义矢量图层
 * 其中style是矢量图层的显示样式 
 */
var style = new ol.style.Style({
  fill: new ol.style.Fill({ //矢量图层填充颜色，以及透明度
    color: 'rgba(255, 255, 255, 0.6)'
  }),
  stroke: new ol.style.Stroke({ //边界样式
    color: '#319FD3',
    width: 1
  }),
  text: new ol.style.Text({ //文本样式
    font: '12px Calibri,sans-serif',
    fill: new ol.style.Fill({
      color: '#000'
    }),
    stroke: new ol.style.Stroke({
      color: '#fff',
      width: 3
    })
  })
});
```
* 天地图
```
var tian_di_tu_road_layer = new ol.layer.Tile({
    title: "天地图路网",
    source: new ol.source.XYZ({
        url: "http://t4.tianditu.com/DataServer?T=vec_w&x={x}&y={y}&l={z}"
    })
});
```
```
var tian_di_tu_annotation = new ol.layer.Tile({
    title: "天地图文字标注",
    source: new ol.source.XYZ({
        url: 'http://t3.tianditu.com/DataServer?T=cva_w&x={x}&y={y}&l={z}'
    })
});
```
```
map.addLayer(tian_di_tu_annotation);
map.addLayer(tian_di_tu_road_layer);
http://t0.tianditu.com/DataServer?
http://t1.tianditu.com/DataServer?
http://t2.tianditu.com/DataServer?
http://t3.tianditu.com/DataServer?  卫星图
http://t4.tianditu.com/DataServer?  路网图
http://t5.tianditu.com/DataServer?
http://t6.tianditu.com/DataServer?
http://t7.tianditu.com/DataServer?
```

* 百度地图
```
<script type="text/javascript">
    var projection = ol.proj.get("EPSG:3857");
    var resolutions = [];
    for(var i=0; i<19; i++){
        resolutions[i] = Math.pow(2, 18-i);
    }
    var tilegrid  = new ol.tilegrid.TileGrid({
        origin: [0,0],
        resolutions: resolutions
    });

    var baidu_source = new ol.source.TileImage({
        projection: projection,
        tileGrid: tilegrid,
        tileUrlFunction: function(tileCoord, pixelRatio, proj){
            if(!tileCoord){
                return "";
            }
            var z = tileCoord[0];
            var x = tileCoord[1];
            var y = tileCoord[2];

            if(x<0){
                x = "M"+(-x);
            }
            if(y<0){
                y = "M"+(-y);
            }

            return "http://online3.map.bdimg.com/onlinelabel/?qt=tile&x="+x+"&y="+y+"&z="+z+"&styles=pl&udt=20151021&scaler=1&p=1";
        }
    });

    var baidu_layer = new ol.layer.Tile({
        source: baidu_source
    });

    var map = new ol.Map({
        target: 'map',
        layers: [baidu_layer],
        view: new ol.View({
            center:  [12959773,4853101],
            zoom: 12
        })
    });
</script>
```

会继续补充！

---

