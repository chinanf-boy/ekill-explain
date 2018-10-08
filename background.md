## background.js

也就做了一件事

``` js
chrome.browserAction.onClicked.addListener(function(tab) {
  chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
    chrome.tabs.sendMessage(tabs[0].id, {}); // 什么信息都没有, 但是类似发送了个信号
  });
});
```

- `chrome.browserAction.onClicked.addListener` 单击浏览器按钮时作出反应。

> [非官方](https://crxdoc-zh.appspot.com/extensions/browserAction#event-onClicked)

- `chrome.tabs.query` 使用 chrome.tabs API 与浏览器标签页交互, 查询
    - `{active: true, currentWindow: true}` 标签页组, 给到第二个函数中的`tabs`参数

> [非官方](https://crxdoc-zh.appspot.com/extensions/tabs#method-query)

- `chrome.tabs.sendMessage` 向指定标签页中的内容脚本发送一个消息

> [非官方](https://crxdoc-zh.appspot.com/extensions/tabs#method-sendMessage) 