<template>
  <div id="app">
    <h1>在线翻译</h1>
    <h4>方便 / 易用 / 快捷</h4>
    <div class="main-content">
      <TranslateForm v-on:my_submit="getText"></TranslateForm>
      <TranslateResult v-bind:result="translated_text"></TranslateResult>
    </div>
  </div>
</template>

<script>

  import TranslateForm from './components/translateForm.vue'
  import TranslateResult from './components/translateResult.vue'
  import {MD5} from './js/md5.js'

  export default {
    name: 'App',
    components: {
      TranslateForm,
      TranslateResult
    },
    data: function () {
      return {
        translateing_text: "",
        language: "",
        translated_text: ""
      }
    },
    methods: {
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

    }
  }
</script>

<style>


  h1 {
    text-align: center;
    letter-spacing: 15px;
  }

  h4 {
    color: gray;
    text-align: center;
  }

  .main-content {
    width: 80%;
    margin: 0 auto;
    display: flex;
    justify-content: space-around;
    align-items: flex-end;
    flex-wrap: wrap;
  }
</style>
