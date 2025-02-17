<template>
  <div class="bar">
    <p class="title">地图</p>
    <div class="right">
      <p>选项1</p>
      <p>选项2</p>
      <p>选项3</p>
      <i class="ri-align-justify"></i>
    </div>
  </div>
</template>

<script setup>

</script>

<style scoped>
@import url('https://cdn.jsdelivr.net/npm/remixicon@4.5.0/fonts/remixicon.css');

  .bar{
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    padding: 0px 10px;
    box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.2);
    background-color: rgb(84, 185, 209);
    margin-bottom: 20px;
    color: aliceblue;
    font-size: 1rem;
    font-weight: 500;
  }

  .right{
    display: flex;
    justify-content: right;
    align-items: center;
  }
  .right p{
    margin-right: 5px;
  }
  i{
    font-size: 1.25rem;
    margin-left: 5px;
  }
  .title {
    padding-left: 5px;
  }
</style>
