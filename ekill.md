## ekill.{js|css}

内容脚本, 插入到网页

### js

这里的主逻辑要从`c.runtime.onMessage.addListener(msgHandler);` 看起

1. 获得[background.js](./background.md)后台传来的信号, 选择性运行函数

2. 选择关还是开, 先开`enable`才能关`disable`

3. `enable`
    - 3.0 active true
    - 3.1 替换所有`a``button``[role=button]`元素的点击监听
    - 好了, 下面是鼠标箭头选择需要除掉的元素
    - 3.2  添加顶级大 `ekill-cursor`class 前缀
    - 3.3 移动 **到** 元素的监听函数: 主要是元素class的add
    - 3.4 移动 **出** 元素的监听函数: 主要是元素class的remove
    - 3.5 点击触发,除掉这个元素 :注意阻止默认行为
    - 3.6 按键`Esc`, 触发`disable`

4. `disable` : 主要是把`enable`影响的,复原
    - 4.0 active false
    - 4.1 恢复所有`a``button``[role=button]`元素的点击监听
    - 好了, 下面是鼠标箭头选择的影响
    - 4.2 去掉顶级大 `ekill-cursor`class 前缀
    - 4.3 清除一些独立的hover操作的`ekill`class
    - 4.4 去掉移动 **到** 元素的监听函数
    - 4.5 去掉移动 **出** 元素的监听函数
    - 4.6 去掉选择元素的点击触发
    - 4.7 去掉按键监听`Esc`

``` js
(function(c, d){ // (chrome, document); 一个是 chrome的扩展api, 一个是document
  let active = false;
  let clickable = [
    d.getElementsByTagName("a"),
    d.getElementsByTagName("button"),
    d.querySelectorAll('[role=button]'),
  ];

  let overHandler = function(e) {
    e.target.classList.add("ekill");
    e.stopPropagation();
  };
  let outHandler = function(e) {
    e.target.classList.remove("ekill");
    e.stopPropagation();
  };
  let clickHandler = function(e) {
    disable();
    e.target.remove();
    e.preventDefault();
    e.stopPropagation();
  };

  let keyHandler = function(e) {
    if (e.key === "Escape") {
      disable();
    }
  }

  let enable = function() {
    active = true; // 3.0

    // 每个 能点的 元素
    clickable.forEach(function(c) {   // 3.1
      for (var i = 0; i < c.length; i++) {
        c[i].onclickBackup = c[i].onclick;
        c[i].addEventListener("click", clickHandler);
      }
    });

    d.documentElement.classList.add("ekill-cursor"); // 3.2
    d.addEventListener("mouseover", overHandler);  // 3.3
    d.addEventListener("mouseout", outHandler); // 3.4
    d.addEventListener("click", clickHandler); // 3.5
    d.addEventListener("keydown", keyHandler, true); // 3.6
  };
  let disable = function() {
    active = false; // 4.0

    clickable.forEach(function(c) { // 4.1
      for (var i = 0; i < c.length; i++) {
        c[i].removeEventListener("click", clickHandler);
        c[i].addEventListener("click", c[i].onclickBackup);
        delete c[i].onclickBackup;
      }
    });

    d.documentElement.classList.remove("ekill-cursor"); // 4.2

    // 清除 一些 独立的 hover 操作的 class
    let orphan = d.querySelector('.ekill');  // 4.3
    if (orphan !== null) {
      orphan.classList.remove("ekill");
    }

    d.removeEventListener("mouseover", overHandler); // 4.4
    d.removeEventListener("mouseout", outHandler); // 4.5
    d.removeEventListener("click", clickHandler); // 4.6
    d.removeEventListener("keydown", keyHandler, true); // 4.7
  };


  let msgHandler = function(message, callback) { // 2. 
    if (active) { // 是否已启动
      disable();
    } else {
      enable();
    }
  };

// c == chrome
  c.runtime.onMessage.addListener(msgHandler); // 1.
})(chrome, document);
```

- `c.runtime.onMessage.addListener` 获得[background.js](./background.md)后台传来的信号, 选择性运行函数

> [非官方](https://crxdoc-zh.appspot.com/extensions/runtime#event-onMessage)



### css

看了, 上面就知道, `enable`会添加一个大`ekill-cursor`class前缀

#### enable后,鼠标在可点击元素上的变化

``` css
.ekill-cursor,
.ekill-cursor a,
.ekill-cursor input,
.ekill-cursor select,
.ekill-cursor button,
.ekill-cursor div[role=button] {
  cursor: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='15' height='15'%3E%3Cpath d='M14 14.7c-.1.2-.3.3-.5.3h-.2l-5.8-2.4L1.7 15c-.3.1-.5 0-.6-.3a.5.5 0 0 1 .2-.7l4.9-2-4.9-2a.5.5 0 1 1 .4-1l5.8 2.5L13.3 9a.5.5 0 1 1 .4.9l-4.9 2 4.8 2a.5.5 0 0 1 .3.7zM12 4.2v.5c0 .2 0 .4-.2.6L10 7v1.2c0 .2-.1.4-.3.4l-2.2.9-2.2-.9a.5.5 0 0 1-.3-.4V7L3.2 5.3a1 1 0 0 1-.2-.6v-.5C3.2 2 4.9.2 7.1 0H8c2.2.2 4 2 4.1 4.2zM6 4a1 1 0 1 0-2 0 1 1 0 0 0 2 0zm1 3a.5.5 0 0 0-1 0v.5a.5.5 0 0 0 1 0V7zm2 0a.5.5 0 0 0-1 0v.5a.5.5 0 0 0 1 0V7zm2-3a1 1 0 1 0-2 0 1 1 0 0 0 2 0z'/%3E%3C/svg%3E"), crosshair !important;
}

```

#### enable后, 鼠标到达所选元素后,样式变化
```css
.ekill {
  filter: opacity(0.2);
  box-shadow: inset 0px 0px 25px rgba(255,0,0,.5);
}
```

