# SDK接入


## 起步

```
<script>
// V1 react vue 暂时使用此版本
(function(e){e._error_storage_=[];e.ERROR_CONFIG={client:"tuia",pageId:"smashg_2",imgUrl:"http://retcode.tuipink.com/report?"};function r(){e._error_storage_&&e._error_storage_.push([].slice.call(arguments))}e.addEventListener&&e.addEventListener("error",r,true);var t=3,n=function e(){var r=document.createElement("script");r.async=!0,r.src="//yun.tuia.cn/tuia/skyeye/skyeye.js",r.crossOrigin="anonymous",r.onerror=function(){t--,t>0&&setTimeout(e,1500)},document.head&&document.head.appendChild(r)};setTimeout(n,1500)})(window);
</script>
```

- 根据项目工程选择对应的SDK版本，将以上选择的SDK版本代码复制到浏览器```</head>```上方

- client 改成自己的项目组名（或业务线名），pageId改成你需要监控页面的id（工程名）

- 对需要监控的js,加入crossOrigin="anonymous",（例如```<script src="//yun.tuia.cn/abc.js" crossOrigin="anonymous"><script>``` 加入同时yun.tuia.cn必须支持跨域）

- 被监控js需要开通跨域

- webpack 插件 http://gitlab2.dui88.com/frontend/html-webpack-plugin-attributes-script
  自动加入crossOrigin="anonymous"

## 说明
```
export interface config {
    client: string, // 项目上报id <required>
    pageId: string, // 项目具体某个活动id <required>
    version: string, // 监控版本 <required>
    imgUrl: string, // 请求url <required>
    level: Number, // 等级(不填默认为0, error级别, 暂时只支持0级别)
    repeat: Number, // 重复上报次数
    ignore: Array<RegExp | Function>, // 过滤条件(使用见example)
    isResource: Boolean, // 是否上报静态资源，true 上报，false不上报
}
```
**required 必填，不填不上报**
**以上都配置在上述e.ERROR_CONFIG对象中**

## 功能
```
1.支持静态资源出错上报
2.支持js执行异常上报
3.支持多次上报上限（缓存配置）
4.支持正则/函数过滤
5.支持ios/android ua 型号机型解析
6.支持生成唯一errorKey
7.支持静态资源出错,详细定位
```

## 示例

**ignore**
```
<script>
(function(e){e._error_storage_=[];e.ERROR_CONFIG={client:"tuia",pageId:"smashg_2",imgUrl:"http://retcode.tuipink.com/report?"};function r(){e._error_storage_&&e._error_storage_.push([].slice.call(arguments))}e.addEventListener&&e.addEventListener("error",r,true);var t=3,n=function e(){var r=document.createElement("script");r.async=!0,r.src="//yun.tuia.cn/tuia/skyeye/skyeye.js",r.crossOrigin="anonymous",r.onerror=function(){t--,t>0&&setTimeout(e,1500)},document.head&&document.head.appendChild(r)};setTimeout(n,1500)})(window);
</script>
```
过滤错误msg 包含aaa的错误。例如报错 Uncaught ReferenceError: aaa is not defined 将不上报。

## 关于sourcemap

可以帮助开发者定位报错源代码

![](//yun.dui88.com/5-mami/1.png)

webpack 这么配置

![](//yun.dui88.com/5-mami/2.jpg)

## 提示

- script error

如果未对所监控的js增加crossOrigin="anonymous"这个标识，将会收集到script error。加入crossOrigin="anonymous"所在域名必须开启跨域，否则会发生错误。

# 可视化

## 地址

在线地址: http://skyeye.dui88.com
## 功能

```
1.支持多时段对比
2.完善的错误处理流程
3.支持sourceMap
4.支持报警
```

## 效果图
![](//yun.duiba.com.cn/tuia/skyeye/img/chart.jpg)
<br />
<br />
![](//yun.duiba.com.cn/tuia/skyeye/img/detail-1.jpg)
<br />
<br />
![](//yun.duiba.com.cn/tuia/skyeye/img/detail-2.jpg)

# 问题反馈

## issue

地址： http://gitlab2.dui88.com/frontend/skyeye-visual/issues
