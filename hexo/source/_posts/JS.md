---
title: 2016-9-11.JS
layout: post
comments: true
tag: 
- javascript
category: javascript
date: 2016.9.11 15:44:14 
---
## 2016-9-11.JS ##
### 1.typeof ###
typeof 运算符   
返回一个用来表示表达式的数据类型的字符串。   
typeof 运算符把类型信息当作字符串返回。   
typeof 返回值有六种可能： "number," "string," "boolean," "object," "function," 和 "undefined."
其中圆括号可以加可以不加。
<!-- more -->
```javascript
typeof(1);//"number"
typeof(NaN);//"number"
typeof(Number.MIN_VALUE);//"number"
typeof(Infinity);//Infinity 属性用于存放表示正无穷大的数值。"number"
typeof("123");//"string"
typeof(true);//"boolean"
typeof(window);//"object"
typeof(document);//"object"
typeof(null);//"object"
typeof(eval);//"function"
typeof(Date);//"function"
typeof(sss);//"undefined"
typeof(undefined);//"undefined"
```
使用typeof主要用来判断变量是否被定义、测试变量是否是方法。
### 2.confirm ###
confirm() 方法用于显示一个带有指定消息和 OK 及取消按钮的对话框。但是不太好看。
```javascript
// 弹框消息
gwwg.h_pop_confirm = function(content,onOk){

    var settings = {
        width: 260,
        height: 120,
        modal: true,
        ok: "确认",
        cancel: "取消"
    };

    if ("undefined" == typeof content) {
        return false;
    }
    if ("undefined" == typeof onOk) {
        var onOk = null;
    }
    var $dialog = $('<div class="xxDialog"><\/div>');
    var $dialogTitle;
    var $dialogClose = $('<div class="dialogClose"><\/div>').appendTo($dialog);
    var $dialogContent;
    var $dialogBottom;
    var $dialogOk;
    var $dialogCancel;
    var $dialogOverlay;

    $dialogContent = $('<div class="dialogContent"><\/div>').appendTo($dialog);

    if (settings.ok != null || settings.cancel != null) {
        $dialogBottom = $('<div class="dialogBottom"><\/div>').appendTo($dialog);
    }
    if (settings.ok != null) {
        $dialogOk = $('<input type="button" class="button" value="' + settings.ok + '" \/>').appendTo($dialogBottom);
    }
    if (settings.cancel != null) {
        $dialogCancel = $('<input type="button" class="button" value="' + settings.cancel + '" \/>').appendTo($dialogBottom);
    }
    if (!window.XMLHttpRequest) {
        $dialog.append('<iframe class="dialogIframe"><\/iframe>');
    }

    $dialog.appendTo("body");
    if (settings.modal) {
        $dialogOverlay = $('<div class="dialogOverlay"><\/div>').insertAfter($dialog);
    }

    var dialogX;
    var dialogY;

    if (settings.title != null) {
        $dialogTitle.text(settings.title);
    }
    $dialogContent.html(content);
    $dialog.css({"width": settings.width, "height": settings.height, "margin-left":parseInt(-settings.width / 2), "z-index": 1000});
    dialogShow();

    if ($dialogTitle != null) {
        $dialogTitle.mousedown(function(event) {
            $dialog.css({"z-index": zIndex ++});
            var offset = $(this).offset();
            if (!window.XMLHttpRequest) {
                dialogX = event.clientX - offset.left;
                dialogY = event.clientY - offset.top;
            } else {
                dialogX = event.pageX - offset.left;
                dialogY = event.pageY - offset.top;
            }
            $("body").bind("mousemove", function(event) {
                $dialog.css({"top": event.clientY - dialogY, "left": event.clientX - dialogX, "margin": 0});
            });
            return false;
        }).mouseup(function() {
            $("body").unbind("mousemove");
            return false;
        });
    }

    if ($dialogClose != null) {
        $dialogClose.bind("click",function() {
            dialogClose();
            return false;
        });

    }

    if ($dialogOk != null) {
        $dialogOk.bind("click",function() {
            if (onOk && typeof onOk == "function") {
                dialogClose();
                onOk();
            } else {
                dialogClose();
            }
            return false;
        });
    }

    if ($dialogCancel != null) {
        $dialogCancel.bind("click",function() {
            dialogClose();
            return false;
        });
    }

    function dialogShow() {
        $dialog.show();
        $dialogOverlay.show();
    }
    function dialogClose() {
        $dialogOverlay.remove();
        $dialog.remove();
    }
    return $dialog;
}
```
大致就是画一个弹框，确认跟取消按钮，调用的时候把弹框插入body中，点击确认执行传入的回调方法。
