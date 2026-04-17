# Optimized_PSA

这是一个基于lnng师傅二开的被动扫描助手
插件ID：75379955-c709-4948-a8ee-f2adafbcc540

## WHY?

因为我习惯于yakit便捷的操作，又无法舍弃burp已经成熟的生态，所以我决定浏览器代理直接发给yakit，然后yakit再发给burp,最后burp再发给xray或者什么的被动扫描器（因为yakit的插件生态其实有点太散了感觉，人机交互并不直观，而burp的插件好用但是不好修改，我没招了），但是我发现原生的代理会把插件流量也发过去，现在市场里有的代理插件又没有去重的功能（其实以前没觉得，直到有一次突然用到了明动的代理转发功能，我才知道了细糠），所以就有了这个插件，最后也是感谢lnng师傅和奇安信。

让token燃烧，让群星陨落！

## 继承了如下重要功能

其实都继承了。。。

黑白名单功能

使用原生poc.HTTP

## 新增了如下功能

#### 多模去重

去重分为三种：

none：不去重

full：根据整个数据包计算一个md5值（这意味着几乎等于不去重，因为sign等因素都会影响）

method_url ：根据请求头和url进行去重（我觉得还可以，挺实用的）

<img width="637" height="691" alt="image" src="https://github.com/user-attachments/assets/6da2811d-c9ca-4f22-bdbf-ea6bd87809b4" />


#### 内存管理和互斥锁

默认缓存只保存10000条流量，第10001条流量会把第1条删掉，因为如果不这么做内存会逐渐升高（依旧没招）如果你的内存条够大也可以加

同时使用了 `sync.NewMutex()` 互斥锁来保护共享缓存 `dedupCache` 和 `dedupQueue`，确保在高并发环境下数据的一致性和安全性

## 效果展示

下面是没有去重前

<img width="744" height="486" alt="image" src="https://github.com/user-attachments/assets/aebcab43-076d-48cb-87fa-ba31cad18a56" />


这是去重后

<img width="766" height="404" alt="image" src="https://github.com/user-attachments/assets/ff07432a-76a3-46d1-a4c2-1413b89a12d2" />


这几个重复是因为有的是http有的是https，并且/oauth和/oauth/在判断中并不同，不过目前来看并不怎么影响，后面有时间会改的。

## 需要改进的地方

后缀黑名单这一块需要优化一下，原插件中这一块其实就没有实现，我会完善一下的。

完善一下路径采集的功能，让/oauth和/oauth/识别为同一个（目前的想法是做一个简单判断，又/的就去掉后md5保存，没有的直接保存）

## 提示

如果要更换去重模式，建议关闭插件再打开插件，这样之前的缓存池子就会刷新，目前测试来看这样是有用的，因为我不会如何实时根据前端选项来关闭池子。
