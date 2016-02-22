# 主要是shap文件发布以后的操作

* 如何操作shap文件做高亮显示？
* shape和geojson的最大不同 需要注意的事项？

---

可以做高亮，但是和 geojson 思路不一致，虽然geojson 和 shapefile 同属矢量文件，但是ol3内置了geojson 格式的解析器，shapefile 没有。


---
##### shape geojson 区别
首先 shapefile 和 geojson 的定位不一致

1. shapefile 是一个重量级的交换格式，可以存储大量的坐标、要素等
2. geojson 一般用于存储少量的信息，且主要用于 客户端和服务器的交换格式
* geojson 和 json 格式完全一致，就是这个原因

---
##### shape为什么不能前端操作
shapefile 使用 javascript 读取的话效率很低，读取解析工作很多，这样会将浏览器拖垮

---

##### 怎么解决
所以一般我们将 shapefile 在后端发布使用地图服务器渲染发布为wms服务这样我们在客户端就可以调用，但是调用以后格式为**图片格式 img/png**

---
##### 为了解决调取要素的问题
WMS 提供了一个操作--->getFeatureInfo 这个方法就是根据客户端获取的坐标在服务器端查询包含这个点的要素，然后返回这个要素的信息：坐标序列、属性等
而 geojson 不一样客户端从服务器端读取完geojson后，或渲染出图形要素，
我们可以根据客户端获取的像素点使用 ol3 的 forEachFeatureAtPixel 直接获取要素
因为 ol3 自己渲染的 geojson，ol3 就清楚geojson的所有信息所以就不用再一次的请求geojson了
**这是两者的不同**

---
##### 服务器geoserver除了发布 在我们刚才的操作中需要做什么配置吗?
**不需要**
geoserver默认开启 这个操作

http://docs.geoserver.org/stable/en/user/data/app-schema/wms-support.html#getfeatureinfo

http://docs.geoserver.org/stable/en/user/services/wms/reference.html#getfeatureinfo（调用方法

geoserver 的 wms 默认开启这个功能，但是要定制返回信息，要做一些服务器的自定义配置,因为默认返回与要素有关的所有信息
**配置就是配置返回的信息,返回那些信息.**
这个配置文件在服务器端一般是一个html，开始可以不用配置直接请求默认的，然后在客户端筛选需要的信息。
另外 wfs 有一个 getFeature 操作，和这个很类似 wfs 一般是对 要素的增删修改使用的服务，

---
#### 问答：
我一直不太明白用getFeatureInfo以后
返回的地图上的某个feature比如河南的轮廓现在我要给他高亮 拿我怎么办自己画一个吗 ？
ol不是不能改变shap的样式吗 ？
getFeatureInfo 返回的信息是单个要素的信息，不是图片，不是shapefile
而是可以是 json或者 xml 等
json我知道可以直接操作
xml好像也可以直接操作了
你获取到结果。直接初始化成一个要素，然后针对它应用一个高亮的样式即可，就是给他一个style就行了
