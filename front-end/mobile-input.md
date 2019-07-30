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

### 输入框问题汇总

#### 键盘高度

### iOS特有的坑

## 总结

