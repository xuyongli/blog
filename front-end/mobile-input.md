# 移动端输入框问题

## 背景

### 需求预沟通

- 产品：我们需要做一个IM，和微信一样
- 我：打扰了，走错片场了，你们继续，，，，，，，
- ....
- 我：这个需求虽然不合理，但是我还是接了。。。。。。

### 输入框

来来来，我们来想一下我们一天中使用频率最高的App——微信，英文名是WeChat，它的聊天界面长啥样子，输入框在最底部（不知道你什么反应，我反正当场就打开了weibo，facebook， twitter等等等的mobile端，就没有一个是把输入框放在最底部的，好的，我当场就对产品发动了强烈的反抗，当然依旧没有成功），输入框默认单行展示，随着用户输入，输入框中的内容自动换行，最多显示3行，超出部分可以滚动，要求可以输入超链接，输入框左边有个插入表情按钮，右边是发送按钮，输入框下面有个checkbox，，，，，

产品：这个需求很简单，怎么实现我不管，反正下个月要上线。

## 技术方案

虽然我很绝望，但是需求还是要做，毕竟上有老，下有小，，，

### 输入框技术选型

来，我们来具体看需求，“输入框内容要自动换行”，排除input；“输入框中可以输入超链接”，排除textarea；还有啥，只有contentEditable为true的div了。

### 插入表情

那我们知道，如果想做各端样式统一的表情的话，必然是用图片展示表情，这样的话，我们的成本就会增加很多，所幸在这一点上说服了产品，可以直接使用emoji，那这里我们只需要找到一个比较全面的emoji字符列表就好了。

## 实现

### 输入框实现

这些能难住CSS高手我吗？不能够吧，直接上代码

- placeholder的实现
- 自动换行，最多3行

```less
.input-editor {
    color: rgba(0, 0, 0, 0.8);
    min-height: 32px;
    max-height: 72px;
    word-break: break-all;
    overflow-y: scroll;
    -webkit-overflow-scrolling: touch;
    -webkit-tap-highlight-color: transparent;
    user-select: text;

    &:empty {
        &::after {
            content: attr(data-placeholder);
            color: rgba(0, 0, 0, 0.24);
        }
    }
}
```

### emoji实现

这里和设计同学确认了一下，表情列表的展示逻辑，一行展示8个效果最好，列表的高度最好与键盘高度一致

```javascript
const EMOJI_SIZE = (window.innerWidth - 24) / 8 - 6; // 一行放八个表情
const emojiStyles = {
    width: EMOJI_SIZE,
    height: EMOJI_SIZE,
    fontSize: `${EMOJI_SIZE / 1.43}px`, // 1.43为line-height
};
```

### checkbox实现

这个没什么好说的，纯CSS实现，背景图，js控制选中态

### 发送按钮

按钮分为可以active与disabled两种状态

## 问题

做完了吗，嗯，是的，我用chrome的模拟器没啥问题了，那我们赶紧真机看一下效果吧，，，迫不及待搓手中，，，，然鹅，，，，，我是不是很菜？这不是真的吧？也许，睡一觉就好了吧！

### 键盘高度获取方案

- Android：键盘弹起时，会触发resize事件，页面的高度会被挤压，也就说输入框只要一直固定在页面底部就可以了。

  监听window的resize事件，记得区分横竖屏的情况，计算resize事件触发前后window.innerHeight的变化，差值即为键盘高度。

- iOS：键盘弹起前后，不会触发resize事件，页面的高度不变，同时会把页面上fixed的元素变为absolute处理（万恶之源）

  这里没有成熟的前端获取键盘高度的方法，所以依赖了客户端的接口。这里有降级方案，输入框在顶部就可以了。


### 重新聚焦input，光标在所有文字的最前面

这个时contentEditable的浏览器支持的bug，解决方法其实也很简单，就是输入框聚焦后，手动的调整光标的位置，关键代码如下

```javascript
const input = this.inputRef.current;
// 矫正光标位置
if (!input || !input.lastChild) return;
const range = document.createRange();
const selection = window.getSelection();
range.selectNodeContents(input.lastChild);
range.collapse(false);
selection.removeAllRanges();
selection.addRange(range);
```

### iOS下，输入内容后再删除至空，不会显示placeholder

经过排查发现，经过用户输入和删除等操作后，光标后会多出一个br标签，这里监听一下KeyUp事件做一下处理就好，关键代码如下

```javascript
const keyCode = event.keyCode;
switch (keyCode) {
    // backspace
    case 8:
    // delete
    case 46:
        if (!this.getContent()) {
            this.setContent('');
        }
        break;
    default:
        // todo
}
```

### 部分安卓手机下，将键盘收起后，输入框不会失去焦点

键盘收起后，也会触发resize事件，这个时候手动触发blur就可以

### 输入内容安全问题

将输入框元素的innerHTML，设置为一个新创建的div的内容，递归遍历所有子节点，将块级元素变为内容加上BR标签，A标签只取href属性，重新生成A标签，保证最后得到的内容是文本、BR标签与A标签的组合。

### 输入内容发送成功后的显示问题


## 总结

