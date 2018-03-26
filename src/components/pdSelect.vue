<template>
  <div class="formBox">
    <div class="input_box" >
      <div class="label_div">选择原因</div>
      <select-normal :is-select-item="isSelectItem"  :placeholder="selectlaceholder" v-on:click="isSelectItem=true" v-on:changeSelectItem="selectItemFn" v-on:isSelectItemShow="isSelectItem=false" :select-item-arr="selectItemArr"></select-normal>
    </div>
    
  </div>
</template>
<script>
  import selectNormal from './selectNormal.vue'
  export default{
    name: 'pdIosSelect',
    data () {
      return {
        selectItemArr:[//下拉选择框数据
          {name:'家庭购物消费',id:'shop'},
          {name:'旅游娱乐',id:'travel'},
          {name:'教育',id:'education'},
          {name:'其他',id:'other'}
        ],
        isSelectItem:false,//下拉选择框是否显示
			  redeemReason:'',//选中的退出原因
			  selectlaceholder:'请选择',
      }
    },
    components: {
      selectNormal
    },
    methods:{
      selectItemFn:function(val){
        console.log('下拉框父');
        console.log(val);
        this.redeemReason=val;
        this.isSelectItem=false;
        this.selectlaceholder='';
      },
    }
  }
</script>
<style lang="less" scoped>
@import '../assets.less';
.formBox{
  background-color: #fff;

  .input_box{
    width: 668*@px1;
    margin:auto;
    background: #fff;
    font-size: 0;
    .label_div{
      color: #666;
      width: 200*@px1;
      display: inline-block;
      font-size: 36*@px1;
      line-height: 3;
    }
    .selectNormal{
      width:6rem;
      font-size: 36*@px1;
    }
  }
}
</style>

