<template>
<div>
  <div class="word">选中的省份：{{province}}</div>
  <div class="word">选中的城市：{{city}}</div>
  <div class="mask" v-if="areaShow">
      <select-box style="position: fixed;bottom: 0;width: 100%">
        <div class="tit">
          <div class="close" @click="closeArea()">确定</div>
        </div>
        <div style="display: flex;">
          <select-item listName='provinceName' :listData="provinceList" v-model="province" v-on:changeVal="changeValFn"></select-item> 
          <select-item listName='areaName' :listData="cityList" v-model="city" v-on:changeVal="changeValFn2" :toBegin="toBegin"></select-item>
        </div>
      </select-box>
  </div>
</div>
  
</template>
<script>
  import selectBox from './selectBox.vue'
  import selectItem from './selectItem.vue'
  import areaData from '../json.json'
  export default{
    name: 'pdIosSelect',
    data () {
      return {
        provinceList:areaData.data,
        cityList:areaData.data[0].areaInfoVOList,
        province:'1',
        city:'',
        toBegin:false,//城市初始化
        areaShow:true,
        areaData:areaData
      }
    },
    components: {
      selectBox,selectItem
    },
    methods:{
      changeValFn:function(data){
				var that=this;
				that.toBegin=true;
				that.provinceCode=data[0];
				var i=data[1];
				that.cityList=that.provinceList[i].areaInfoVOList;
				that.cityCode=that.cityList[0].code;
				that.city=that.cityList[0].areaName;
			},
			changeValFn2:function(data){
				var that=this;
				that.toBegin=false;
				that.cityCode=data[0];
				
      },
      //关闭弹出层
			closeArea:function(){
				this.areaShow=false;
			}
    }
  }
</script>
<style lang="less" scoped>
*{box-sizing: border-box;}
.word{
  font-size: 0.4rem;
}
</style>

