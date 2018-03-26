<template>
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
</template>
<script>
  import selectBox from './selectBox.vue'
  import selectItem from './selectItem.vue'
  export default{
    name: 'pdIosSelect',
    data () {
      return {
        cityList:Array.from({length: 12}, (value, index) => 1 + index),
        provinceList:[{areaName}],
        province:'1',
        city:'',
        toBegin:false,//城市初始化
        areaShow:true
      }
    },
    components: {
      selectBox,selectItem
    },
    created(){
      this.test();
    },
    methods:{
      changeValFn:function(data){
				var that=this;
				that.toBegin=true;
				console.log('省份测试:');
				console.log(data);
				// that.provinceCode=data[0];
				//var i=data[1];
				// that.cityList=that.allAddressList[i].areaInfoVOList;
				// that.cityCode=that.cityList[0].code;
				// that.city=that.cityList[0].areaName;
				
			},
			changeValFn2:function(data){
				var that=this;
				that.toBegin=false;
				console.log('城市测试:');
				console.log(data);
				// that.cityCode=data[0];
				
      },
      //关闭弹出层
			closeArea:function(){
				this.areaShow=false;
			},
      test:function(){
        console.log(this.cityList);
        console.log(this.provinceList);
      }
    }
  }
</script>
<style lang="less" scoped>
@import '../assets.less';

</style>

