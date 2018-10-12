<template>
  <div class="translateResult">
    <h2>{{result}}</h2>
  </div>
</template>

<script>
  export default {
    name: 'translateResult',
    data() {
      return {}
    },
    props:['result']
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .translateResult{
    width: 45%;
    height: 300px;
    min-width: 300px;
    margin: 10px 10px;
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
