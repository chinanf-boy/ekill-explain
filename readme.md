# rhardih/ekill [![explain]][source] [![translate-svg]][translate-list]

<!-- [![size-img]][size] -->

[explain]: http://llever.com/explain.svg
[source]: https://github.com/chinanf-boy/Source-Explain
[translate-svg]: http://llever.com/translate.svg
[translate-list]: https://github.com/chinanf-boy/chinese-translate-list
[size-img]: https://packagephobia.now.sh/badge?p=Name
[size]: https://packagephobia.now.sh/result?p=Name

「 Chrome 和 Firefox 插件可快速删除网页上的元素 」

---

## explain 🀄️

<!-- doc-templite START generated -->
<!-- time = '2018-10-07' -->
<!-- name = 'rhardih' -->
<!-- repo = 'ekill' -->
<!-- commit = '7b83eae9c919de2f9b69fac95bc6e51b26de0490' -->

| 版本     | 与日期        | 最新更新   | 更多               |
| -------- | ------------- | ---------- | ------------------ |
| [commit] | ⏰ 2018-10-07 | ![version] | [源码解释][source] |

[commit]: https://github.com/rhardih/ekill/tree/7b83eae9c919de2f9b69fac95bc6e51b26de0490
[version]: https://img.shields.io/npm/v/ekill.svg

<!-- doc-templite END generated -->

### 中文

- [zh readme.md](./zh.md)

## 生活

[help me live , live need money 💰](https://github.com/chinanf-boy/live-need-money)

---

> 一个 Chrome 扩展其实就是一个配置文件 `manifest.json` 和一系列 HTML、CSS、JS、图片文件的集合

## manifest.json

> 因为第一次, 扩展explain, 详细点

``` json
{
  "name": "ekill",
  "version": "1.4",
  "description": "Remove unwanted elements from a page quickly!",
  "manifest_version": 2,
  "browser_action": {
    "default_icon": { // 默认显示在工具栏的图标
      "16": "skull-and-bones-16.png",
      "48": "skull-and-bones-48.png",
      "128": "skull-and-bones-128.png"
    }
    // "default_popup": "popup.html"
    // 加多个提示, 这个是 点击 图标,所显示的页面
  },
  "permissions": [ // 请求权限
    "activeTab" // 当前标签页
  ],
  "background": { // 后台网页
    "scripts": ["background.js"],
    "persistent": false // 不一直存在: 推荐
  },
  "content_scripts": [ // 内容脚本
    {
      "matches": ["<all_urls>"],
      "js": ["ekill.js"],
      "css": ["ekill.css"]
    }
  ],
  "icons": { // 安装过程中和Chrome网上应用店中使用
    "16": "skull-and-bones-16.png",
    "48": "skull-and-bones-48.png",
    "128": "skull-and-bones-128.png"
  },
  "commands": { // 命令挂钩
    "_execute_browser_action": {
      "suggested_key": {
        "default": "Ctrl+K",
        "mac": "MacCtrl+K"
      }
    }
  }
}
```

- [更多manifest.json信息:官方](https://developer.chrome.com/extensions/manifest)

>  还有个 [非官方中文网站](https://crxdoc-zh.appspot.com/apps/manifest)

- [ ] [background](#background)
- [ ]] [content_scripts](#content_scripts)
- [x] [commands](#commands)

### background

扩展程序通常需要有一个长时间运行的脚本来管理一些任务或状态，而后台网页就是为这一目的而设立。

``` json
  "background": { // 后台网页
    "scripts": ["background.js"],
    "persistent": false
  }
```

- [x] [background.js](./background.md) 信号发送到内容脚本

- [官方](https://developer.chrome.com/extensions/background_pages) || [非官方:中文](https://crxdoc-zh.appspot.com/extensions/background_pages)

### content_scripts

内容脚本是在网页的上下文中运行的 JavaScript 文件

``` json
  "content_scripts": [ // 内容脚本
    {
      "matches": ["<all_urls>"], // 在所有url中文插入 下面 js + css
      "js": ["ekill.js"],
      "css": ["ekill.css"]
    }
  ]
```

- [x] [ekill.js](./ekill.md#js)
- [x] [ekill.css](./ekill.md#css)

- [官方](https://developer.chrome.com/extensions/content_scripts) || [非官方:中文](https://crxdoc-zh.appspot.com/extensions/content_scripts)

### commands

'_execute_browser_action'和'_execute_page_action'命令保留用于打开扩展程序弹出窗口的操作

``` json
  "commands": { // 命令挂钩
    "_execute_browser_action": {
      "suggested_key": {
        "default": "Ctrl+K",
        "mac": "MacCtrl+K"
      }
    }
  }
```

- [官方](https://developer.chrome.com/extensions/commands) 

### 其他

#### package.sh

作者的打包sh脚本, 不长也放出来

``` sh
#!/usr/bin/env bash

mkdir tmp
git archive HEAD --format=zip > tmp/ekill.zip # 运用git,从git命名树创建文件存档, 打包git存储库

pushd tmp # pushd：切换到作为参数的目录，并把原目录和当前目录压入到一个虚拟的堆栈中

zip -d ekill.zip .gitignore # 删除 压缩多余文件
zip -d ekill.zip README.md
zip -d ekill.zip package.sh
zip -d ekill.zip example.gif

popd # popd : 弹出pushd的堆

if [ ! -f ekill.zip ]; # 看看有没有旧的, 删除
then
  rm ekill.zip
fi

mv tmp/ekill.zip . # 移到 根目录
rm -rf tmp # 删掉缓存目录

zipinfo ekill.zip # zip 信息
```