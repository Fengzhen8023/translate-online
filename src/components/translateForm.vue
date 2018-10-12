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

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #translateForm {
    width: 45%;
    min-width: 300px;
    margin: 10px 10px;
    /*background-color: #0049ff;*/
  }

  .option-box {
    width: 100%;
    min-width: 300px;
    height: 50px;
    position: relative;
    z-index: 99;
  }

  .option-result {
    width: 150px;
    height: 38px;
    display: inline-block;
    text-align: center;
    line-height: 38px;
    border-radius: 5px;
    border: 1px solid #666;
    color: #666;
    box-sizing: border-box;
    font-size: 18px;
    font-family: "微软雅黑";
    cursor: pointer;
  }

  .option-list {
    width: 300px;
    position: absolute;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-content: flex-start;
    top: 50px;
    left: 0px;
    background-color: #fff;
    border-radius: 10px;
    display: none;
  }

  .option-list p {
    display: inline-block;
    width: 45%;
    height: 30px;
    text-align: center;
    line-height: 30px;
    border-radius: 5px;
    margin: 10px 5px;
    cursor: pointer;

  }

  .option-list p:hover {
    background-color: rgba(255, 50, 50, 0.5);
  }

  input[type='button'] {
    display: inline-block;
    width: 108px;
    height: 38px;
    background-color: #e02433;
    border: none;
    border-radius: 5px;
    color: #fff;
    font-size: 20px;
    font-family: "微软雅黑";
    box-sizing: border-box;
    cursor: pointer;
  }

  .my-form {
    width: 100%;
    position: relative;
    top: 4px;
    border-radius: 5px;
    margin: 0 auto;
  }

  .my-textarea {
    width: 100%;
    height: 300px;
    min-width: 300px;
    margin: 0 auto;
    border: none;
    border-radius: 10px;
    font-size: 26px;
    color: #666;
    padding: 40px 20px;
    box-sizing: border-box;
    font-family: "微软雅黑";
    resize: none;
    background-color: rgb(242, 242, 242);
  }
</style>
