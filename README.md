## 一、项目简介

学习Vue也有一段时间了，这几天使用Vue的相关知识完成了一个在线翻译的项目。项目整体上很简单，但是里面包含的一些知识还是很多的。如果你是一名Vue小白，建议你阅读这篇文章。通过参考这个项目，你可以了解从零开始搭建Vue项目，到最终的打包上线的完整过程。

真个项目中使用的技术栈如下：
- 使用Vue脚手架快速搭建项目
- 在Vue组件中引入外部的js脚本
- 在Vue项目中引入jQuery库
- 调用外部API，这里使用的是百度的翻译API
- 组件之间的数据传递

最后展示一下这个项目。这是一个在线翻译的项目，可以通过选择，将中文翻译成英文，日文，俄文等其他语言。

![](https://upload-images.jianshu.io/upload_images/3879603-54d9fdaaf757a6f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/3879603-a18b533bc182760b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/3879603-e8c61193d094972e.gif?imageMogr2/auto-orient/strip)


## 二、项目的具体介绍

##### 1.搭建项目，安装vue-resource，配置jQuery库

首先我们要做的是使用Vue脚手架搭建一个项目，因为我的这个项目中使用到了vue-resource组件和jQuery第三方库，所以建议你在完成项目搭建之后就安装vue-resource组件和配置jQuery，如果你不会配置jQuery，可以参考文章：[Vue笔记——Vue组件中引入jQuery]()。


##### 2.申请并掌握百度翻译API接口

项目中使用到的百度提供的免费翻译API，如果之前没有使用过这个API，可以到[百度翻译开放平台](http://api.fanyi.baidu.com/api/trans/product/index)进行了解。

对百度提供的API不熟悉的同学，一定要沉下心来看一看他的技术文档。这个文档是很详细的，会跟你讲解如果使用API。大致的思路如下：
1. 首先你要注册一个百度翻译开放平台的账号，然后申请开发者权限，获取一个APP ID和秘钥。

![](https://upload-images.jianshu.io/upload_images/3879603-81bbaf8bcdb73a46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.然后下载它的实例DEMO，百度翻译的API是需要给数据MD5加密的，所以在它的DEMO里面会有一个md5.js脚本文件，我们需要在组件中引入它对数据进行加密。如果你不知道如何在Vue组件中引入外部js脚本文件，可以参考文章：[Vue笔记——在Vue项目中引入外部js文件](https://www.jianshu.com/p/35ea4c58cd66)。

##### 3.开始编写项目代码

上述准备工作做好之后，我们就可以进行编写我们的项目代码。

我这里使用到了两个子组件，其中一个子组件 **translateForm** 是用来接收用户输入的待翻译文字，以及用户选择的翻译模式。用户填写好这些信息之后，点击【翻译】，相关的数据就会传递到这个 **translateForm** 组件的父组件，也就是 **App.vue** 组件中。

**translateForm** 组件的主要代码：

```vue
<template>
    <div id="translateForm">

        <!-- 用户选择的翻译模式 -->
        <div class="option-box">
            <span class="option-result" v-on:click="showList">{{ option_result }}</span>
            <div class="option-list">
                <p v-for="(option,index) in option_list" v-on:click="selectOption(index)">{{ option }}</p>
            </div>
            <input type="button" value="翻译" v-on:click="my_translate()">
        </div>

        <!-- 用户输入的要翻译的文字 -->
        <form class="my-form">
            <textarea class="my-textarea" v-model="translating_text" placeholder="请输入要翻译的文字"
                      v-on:keydown.enter="my_translate"></textarea>
        </form>
    </div>
</template>


<script>
    import $ from 'jquery'

    export default {
        name: 'translateForm',
        data() {
            return {
                translating_text: "",
                language: "en",
                option_result: "中文 > 英文",
                option_list: ["中文 > 英文", "中文 > 日文", "中文 > 韩文", "中文 > 法文", "中文 > 泰文", "中文 > 德文", "中文 > 阿拉伯文", "中文 > 俄文", "中文 > 意大利文", "中文 > 丹麦文"],
                language_list: ["en", "jp", "kor", "fra", "th", "de", "ara", "ru", "it", "dan"]
            }
        },
        methods: {
            // 用户点击【翻译】，触发自定义事件，将数据传递给父组件
            my_translate: function () {
                this.$emit("my_submit", this.translating_text, this.language)
                event.preventDefault();
            },

            // 下使用jQuery库来个翻译选项加一些下拉的动画
            showList: function () {
                if ($('.option-list').css('display') != 'none') {
                    $('.option-list').slideUp();
                }
                else {
                    $('.option-list').slideDown();
                }
            },

            // 通过用户选择的翻译模式，设置相应的数据
            selectOption: function (index) {
                $('.option-list').slideUp();
                this.language = this.language_list[index];
                this.option_result = this.option_list[index];
            }
        }
    }

</script>
```

在 **App.vue** 组件接收到子组件传递过来的数据之后，开始请求百度翻译API，进行翻译。这里因为是跨域请求数据，所以要使用jsonp，如果你不了解如何跨域请求数据，可以参考文章：[Vue笔记——解决Vue请求No 'Access-Control-Allow-Origin' 错误](https://blog.csdn.net/fengzhen8023/article/details/83035596)

 **App.vue** 组件主要的代码如下：

```vue 
getText: function (text, lan) {
  this.translateing_text = text;
  this.language = lan;

  // 初始化要传递给API的数据
  var appid = '这里填写是你申请的APP ID';
  var key = '这里填写是你申请的秘钥';
  var salt = (new Date).getTime();
  var query = this.translateing_text;
  var from = 'zh';
  var to = this.language;
  var str1 = appid + query + salt + key;
  var sign = MD5(str1);

  this.$http.jsonp("http://api.fanyi.baidu.com/api/trans/vip/translate?q=" + query + "&from=" + from + "&to=" + to + "&appid=" + appid + "&salt=" + salt + "&sign=" + sign, {}, {
      headers: {},
      emulateJSON: true
  })
      .then(function (response) {
          this.translated_text = response.body.trans_result[0].dst;
          console.log(this.translated_text);
      })
}
```

通过请求翻译API，可以获得翻译后的结果，接收到这个结果之后，将数据传递给另外一个子组件 **translateResult** 进行渲染输出。

##### 4.完成项目，打包上线

不熟悉Vue的同学心里面可能会有这样一个疑问，我使用Vue脚手架搭建的项目都是特别大的，而且每次运行都是使用`npm run dev`来开启服务才能收使用。如果我完成一个项目，想把它传到服务器上面，要怎么做呢？还要不要每次运行的时候都要使用`npm run dev`来开启服务。

我们使用Vue脚手架搭建的项目是依赖于node.js的。所以我们在开发的时候开启服务之后才能看得到我们的项目，但是如果我们完成了一个项目，就可以使用打包工具把我们的项目给打包成一个静态文件，放到我们的服务器上，不用开启任何服务，就可以直接访问。

关于如何打包Vue项目使之上线，可以参考文章:[vue.js项目打包上线](https://blog.csdn.net/crazywoniu/article/details/74065721)

## 三、总结

以上只是对这个项目的这个项目的简要概括，列出了主要的代码，如果大家对这个项目感兴趣的话，可以直接查看源代码。

源代码在使用的时候，一定要提前申请你的百度翻译API，将申请到的APP ID和秘钥填写到对应的位置，即可使用。



```vue

getText: function (text, lan) {
    this.translateing_text = text;
    this.language = lan;

    // 初始化要传递给API的数据
    var appid = '在这输入你的百度翻译APP ID';
    var key = '在这输入你的百度翻译的秘钥';
    var salt = (new Date).getTime();
    var query = this.translateing_text;
    var from = 'zh';
    var to = this.language;
    var str1 = appid + query + salt + key;
    var sign = MD5(str1);

    this.$http.jsonp("http://api.fanyi.baidu.com/api/trans/vip/translate?q=" + query + "&from=" + from + "&to=" + to + "&appid=" + appid + "&salt=" + salt + "&sign=" + sign, {}, {
    headers: {},
    emulateJSON: true
    })
    .then(function (response) {
    	this.translated_text = response.body.trans_result[0].dst;
        console.log(this.translated_text);
    })
    }
```



