---
title: flutter中使用webview-解决依赖问题
tags: flutter问题
categories: flutter问题
toc: true
date: 2019-01-10 15:31:38
---

为什么要发这篇文章呢，因为小编创建项目的时候发现了哥问题，是在test目录的一个

```dart
import 'package:flutter_webview_plugin/flutter_webview_plugin.dart';
```
导入失败问题，

改了很多地方都没解决，最终发现在依赖里面，这个是调用第三方插件所以要在依赖那边设置。

然后pakeages get,才行


首先要安装一个插件：*** flutter_webview_plugin ***

```yaml
dependencies: flutter_webview_plugin: ^0.2.1+2
```

使用方法：

```dart
new MaterialApp(
      routes: {
        "/": (_) => new WebviewScaffold(
              url: "https://www.google.com",
              appBar: new AppBar(
                title: new Text("Widget webview"),
              ),
            )
      },
    );
```
`FlutterWebviewPlugin` 插件提供一个链接到唯一webview的单一实例，这样你就可以在app中的任何地方控制webview，比如监听事件：

```dart
final flutterWebviewPlugin = new FlutterWebviewPlugin();
//  监听url地址改变事件
flutterWebviewPlugin.onUrlChanged.listen((String url) {
  
});
```

```dart
//  监听页面滚动事件
final flutterWebviewPlugin = new FlutterWebviewPlugin();
flutterWebviewPlugin.onScrollYChanged.listen((double offsetY) { 
  
});

flutterWebviewPlugin.onScrollXChanged.listen((double offsetX) { 
 
});
```

隐藏webview：
```dart
final flutterWebviewPlugin = new FlutterWebviewPlugin();  

flutterWebviewPlugin.launch(url, hidden: true);
```
关闭webview：
```dart
flutterWebviewPlugin.close();
```

画一个内部矩形webview：
```dart
final flutterWebviewPlugin = new FlutterWebviewPlugin();  

flutterWebviewPlugin.launch(url,
                  fullScreen: false,
                  rect: new Rect.fromLTWH(
                      0.0, 
                      0.0, 
                      MediaQuery.of(context).size.width, 
                      300.0));
```

注意：webview并不存在于widget树中，所以你不能在webview中使用如snackbars, dialogs ...这些通知交互widget，更详细一些使用方法可以点击这里；

最后，这是一个使用例子

```dart
import 'package:flutter/material.dart';
import 'package:flutter_webview_plugin/flutter_webview_plugin.dart';

class WebViewExample extends StatefulWidget {
  @override
  _WebViewExampleState createState() => _WebViewExampleState();
}

class _WebViewExampleState extends State<WebViewExample> {
  TextEditingController controller = TextEditingController();
  FlutterWebviewPlugin flutterWebviewPlugin = FlutterWebviewPlugin();
  var urlString = "https://google.com";

  launchUrl() {
    setState(() {
      urlString = controller.text;
      flutterWebviewPlugin.reloadUrl(urlString);
    });
  }

  @override
  void initState() {
    super.initState();

    flutterWebviewPlugin.onStateChanged.listen((WebViewStateChanged wvs) {
      print(wvs.type);
    });
  }

  @override
  Widget build(BuildContext context) {
    return WebviewScaffold(
      appBar: AppBar(
        title: TextField(
          autofocus: false,
          controller: controller,
          textInputAction: TextInputAction.go,
          onSubmitted: (url) => launchUrl(),
          style: TextStyle(color: Colors.white),
          decoration: InputDecoration(
            border: InputBorder.none,
            hintText: "Enter Url Here",
            hintStyle: TextStyle(color: Colors.white),
          ),
        ),
        actions: <Widget>[
          IconButton(
            icon: Icon(Icons.navigate_next),
            onPressed: () => launchUrl(),
          )
        ],
      ),
      url: urlString,
      withZoom: false,
    );
  }
}
```