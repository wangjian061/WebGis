# Openglaers3

### 引用openlayers3的 js css
[openlayers3官方网站](http://openlayers.org/)

```
<head>
    <title>Rotation example</title>
    <link rel="stylesheet" href="http://openlayers.org/en/v3.0.0/css/ol.css" type="text/css">
    <script src="http://openlayers.org/en/v3.11.2/build/ol.js"></script>
</head>

```
- 创建一个地图

```
var map;
  function initMap() {
      map = new ol.Map({
          layers: [
              new ol.layer.Tile({
                  source: new ol.source.OSM()
              })
          ],
          target: 'map',
          }),
          view: new ol.View({
              center: ol.proj.fromLonLat([103.986908, 1.353199]),
              rotation: 68*Math.PI/180,
              zoom: 18
          })
      });
  }

```
1. ol.Map 属性layers=[数组] 数组种包含的都是图层的名字用,隔开
2. target 容器需要创建一个div承载地图
3. view 展示在页面上的视图这里设置了它的中心和偏移

***
### 图层的加载

- #### 天地图

```
//加载天地图服务
 var tian_di_tu_road_layer = new ol.layer.Tile({
     title: "天地图路网",
     source: new ol.source.XYZ({
         //     url: "http://t4.tianditu.com/DataServer?T=vec_w&x={x}&y={y}&l={z}"
         url: "http://t3.tianditu.com/DataServer?T=img_w&x={x}&y={y}&l={z}"
     }),
     visible: true
 });
 var tian_di_tu_annotation = new ol.layer.Tile({
     title: "天地图文字标注",
     source: new ol.source.XYZ({
         url: 'http://t3.tianditu.com/DataServer?T=cva_w&x={x}&y={y}&l={z}'
     }),
     visible: true
 });

```
#### 参数解释

 * visible 是否可见
 * ol.source.XYZ 天地图本事采用的加载方式这里做了算法偏移
***

- #### 创建普通wms图层

解释普通的WMS图层由Geoserver发布承德WMS服务

```
var untiled = new ol.layer.Image({
    source: new ol.source.ImageWMS({
        ratio: 1,
        url: 'http://localhost:8080/geoserver/wangjian/wms',
        params: {
            'FORMAT': format,
            'VERSION': '1.1.1',
            STYLES: '',
            LAYERS: 'wangjian:TwoClasses_new55_CGCS2000',
        }
    })
});
var tiled = new ol.layer.Tile({
    visible: false,
    source: new ol.source.TileWMS({
        url: 'http://localhost:8080/geoserver/wangjian/wms',
        params: {
            'FORMAT': format,
            'VERSION': '1.1.1',
            tiled: true,
            STYLES: '',
            LAYERS: 'wangjian:TwoClasses_new55_CGCS2000',
        }
    })
});

```
###### 解释：
普通的wms发布以后回生成两种：

一种是整个图一起加载img

一种是瓦片形式加载的Tile

###### 参数讲解：
visible 设置是否隐藏/可见
source  资源
params 参数
* Params :
- format   img/png   我们用的就是这种格式

- version  1.1.1   这个暂时不用管根据生成的wms服务连接填写就行

- style    图层的样式 暂时不用管，一般在shape文件时候才会用到，wms服务的栅格文件基本用不到

- Layers   生成的wms服务连接Layers对应tif文件名字组成形式 workplace：storeName

- 有了这几个参数我们基本就可以加载并看到一个地图了


***
- #### 创建一个Marker
```
//向地图添加一个Marker
function addmarker(lat, lng) {

    var iconFeature = new ol.Feature({
        //注意如果是4326的坐标请启用下面的这个方法，直接用的3857的方法应下下面的
        // geometry: new ol.geom.Point(ol.proj.transform([lng,lat],'EPSG:4326', 'EPSG:3857')),
        geometry: new ol.geom.Point([lat, lng]),
        // 此处设置了一个name可以作为识别marker的一个标志
        name: 'camera'
    });

    var iconStyle = new ol.style.Style({
        image: new ol.style.Icon(({
            anchor: [0, 0],//注意地图上的偏数回很大，参数0可以为小数
            anchorXUnits: 'fraction',
            anchorYUnits: 'pixels',
            opacity: 1,
            src: 'data/icon.png'//确认你的文件下有这个icon图标
        }))
    });
    iconFeature.setStyle(iconStyle);
    var vectorSource = new ol.source.Vector({
        features: [iconFeature]
    });

    vectorLayer = new ol.layer.Vector({
        source: vectorSource
    });
    map.addLayer(vectorLayer);
}

```
* 解释：

- 穿件一个Marker实际上是点并给它加上一个Marker转变成一个feature附在一个矢量图层上最后把这个
图层添加到地图上边就显示出来了 map.addLayer("图层名字")，这里的图层名字请区分不是wms连接的
tif名字，同样我们想移除 map.removeLayers("图层名字").

***

**插入**

绘制了一个突变那么矢量图成也是一样的原理

- #### geometry

```
function addPolygon() {
        // 根据坐标画多边形
        var overlayStyle = new ol.style.Style({
            stroke: new ol.style.Stroke({//设置边线颜色和粗细
                color: '#FF4040',
                width: 3
            }),
            fill: new ol.style.Fill({//设置围成的多边形的填充色
                color: 'rgba(255,255,255,0)'
            }),
            text: new ol.style.Text({//设置文字颜色暂时不用
                font: '12px Calibri,sans-serif',
                //text: text,
                fill: new ol.style.Fill({
                    color: '#000'
                }),
                stroke: new ol.style.Stroke({
                    color: '#f00',
                    width: 30
                })
            })
        })
        var polyCoords = [];
        var minX = "109.97718799476587";
        var maxX = "117.18520484374086";
        var minY = "31.0409413066997";
        var maxY = "36.72408289756227";
        var coords = (minX + "," + minY + " " + maxX + "," + minY + " " + maxX + "," + maxY + " " + minX + "," + maxY).split(' ');
        for (var i in coords) {
            var c = coords[i].split(',');
            polyCoords.push(ol.proj.transform([parseFloat(c[0]), parseFloat(c[1])], 'EPSG:4326', 'EPSG:3857'));
        }
        var feature = new ol.Feature({
            geometry: new ol.geom.Polygon([polyCoords])
        })
        var layer = new ol.layer.Vector({
            source: new ol.source.Vector({
                features: [feature]
            }),
            style: overlayStyle
        });
        map.addLayer(layer);

    }

```


- #####  上边的方法注释已经解释的很清楚了，我们主要看  
```
new ol.geom.Polygon([x,y]);
//参数是一个xy的坐标集，可以为多个xy组成的排列顺序按照你要画的图形就可以

```
这是创建一个多变性polygon line point 等的方法 Geometry是地图的构成元素，我们利用上面的方法
把多边形添加成了地图的元素在制作成一个feature特征，再添加到一个屠城里边，再添加到地图里边。。。
。。。你晕了吗。。
