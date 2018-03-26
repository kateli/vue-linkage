<template>
  <div class="pd-select-box">
    <slot></slot>
  </div>
  
</template>
<script>
  export default{
  }
</script>
<style scoped>
  .pd-select-box {
    
    background-color: #fff;
  }
  .pd-select-box .tit{
    height: 1rem;
    display: flex;
    justify-content: flex-end;
    padding:0 0.46666667rem;
    width: 10rem;
    line-height: 1rem;
    border:1px solid #dedede;
  }
  .pd-select-box .tit .close{
    color:#007aff;
    line-height: 1rem;
  }
</style>
