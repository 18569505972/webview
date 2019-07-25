# webview
H5与IOS、Andriod交互
##交互方式
### url传参
通过url参数实现app与H5之间的信息交互。
### 全局调用
app直接调用H5全局函数，H5调用APP注入的全局对象。
### JSBridge
```
// h5与原生桥
var jsBridge = {
    // WebViewJavascriptBridge初始化
    init: function(callback) {
        // 安卓
        if (window.WebViewJavascriptBridge) {
            callback(window.WebViewJavascriptBridge)
        } else {
            document.addEventListener('WebViewJavascriptBridgeReady', function() {
                callback(WebViewJavascriptBridge)
            }, false);
        }
        // IOS
        if (window.WebViewJavascriptBridge) {
            return callback(window.WebViewJavascriptBridge);
        }
        if (window.WVJBCallbacks) {
            return window.WVJBCallbacks.push(callback);
        }
        window.WVJBCallbacks = [callback];
        var wvjbIframe = document.createElement('iframe');
        wvjbIframe.style.display = 'none';
        // 通过iframe加载初始化拦截链接https://__bridge_loaded__
        wvjbIframe.src = 'https://__bridge_loaded__';
        document.body.appendChild(wvjbIframe);
        setTimeout(function() { document.documentElement.removeChild(WVJBIframe) }, 0)
    },
    /*
     js调用APP，  
     name：方法名，  
     data：js传递数据，
     callback：js回调函数，
     result：APP返回结果
     */
    callHandler: function(name, data, callback) {
        this.init(function(bridge) {
            bridge.callHandler(name, data, function(result) {
                callback(result);
            });
        });
    },
    /*
     APP调用js，  
     name：方法名，  
     callback：App回调函数，
     result：App返回结果
     */
    registerHandler: function(name) {
        this.init(function(bridge) {
            bridge.registerHandler(name, function(result, callback) {
                callback('已收到数据');
            })
        })
    }
}
```

