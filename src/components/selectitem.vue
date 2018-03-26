<template>
  <div class="pd-select-item" @touchstart="itemTouchStart" @touchmove="itemTouchMove" @touchend="itemTouchEnd">
    <div class="pd-select-line"></div>
    <div class="pd-select-list">
      <ul class="pd-select-ul" ref="list">
        <li v-if="listName=='provinceName'" data-name="省" class="pd-select-list-item" v-for="el,index in renderData " :class="{'hidden':setHidden(el.index)}" :key="index" >{{el.value.provinceName}}</li>
        <li v-if="listName=='areaName'" id="cityItem" data-name="市" class="pd-select-list-item" v-for="el,index in renderData " :class="{'hidden':setHidden(el.index)}" :key="index" >{{el.value.areaName}}</li>
      </ul>
    </div>
    <ul class="pd-select-wheel" ref="wheel">
      <li v-if="listName=='provinceName'" data-name="省" class="pd-select-wheel-item" :class="{'hidden':setHidden(el.index)}" :style="setWheelItemDeg(el.index)" :index="el.index" v-for="el,index in renderData " :key="index">{{el.value.provinceName}}</li>
      <li v-if="listName=='areaName'" data-name="市" class="pd-select-wheel-item" :class="{'hidden':setHidden(el.index)}" :style="setWheelItemDeg(el.index)" :index="el.index" v-for="el,index in renderData " :key="index">{{el.value.areaName}}</li>
    </ul>
  </div>
</template>
<script>
  var rem=parseFloat((document.getElementsByTagName('html')[0].style.fontSize).replace('px',''));
  console.log(rem);
  export default{
    name: 'pdSelectItem',
    data () {
      return {
        spin: {start: -9, end: 9, branch: 9},
        finger: {startY: 0, lastY: 0, startTime: 0, lastTime: 0, transformY: 0}
      }
    },
    props: {
      listData: {
        type: Array,
        required: true
      },
      type: {
        type: String,
        default: 'line'
      },
      value: {},
      listName:{
        default: ''
      },
      toBegin:{//初始化
        default:false
      }
    },
    computed: {
      renderData () {
        let temp = []
        for (let k = this.spin.start; k <= this.spin.end; k++) {
          let data = {
            value: this.getSpinData(k),
            index: k
          }
          temp.push(data)
        }
        return temp
      }
    },
    watch:{
        toBegin:function(val){
          console.log('this.toBegin   @@@@'+this.listName+'####'+val)
          if(this.listName=='areaName'){
            
            if(val==true){
                this.setListTransform(0,0,'end')//0,0,'end',1000
                this.setStyle(0,'end')
                this.getPickValue(0)
            }
          }
          
        }
    },
    mounted () {
      /* 事件绑定 */
      // this.$el.addEventListener('touchstart', this.itemTouchStart)
      // this.$el.addEventListener('touchmove', this.itemTouchMove)
      // this.$el.addEventListener('touchend', this.itemTouchEnd)
      /* 初始化状态 */
      let index = this.listData.indexOf(this.value)
      if (index === -1) {
        console.warn('当前初始值不存在，请检查后listData范围！！')
        this.setListTransform()
        this.getPickValue(0)
      } else {
        // 34 => 68
        let move = index * (68/75*rem).toFixed(2);
        /* 因为往上滑动所以是负 */
        this.setStyle(-move)
        this.setListTransform(-move, -move)
      }
    },
    methods: {
      /* 根据type 控制滚轮显示效果 */
      setHidden (index) {
        if (this.type === 'line') {
          return index < 0 || index > this.listData.length - 1
        } else {
          return false
        }
      },
      setWheelItemDeg (index) {
        // 100 => 200
        return {
          transform: `rotate3d(1, 0, 0, ${-index * 20 % 360}deg) translate3d(0, 0, ${(200/75*rem).toFixed(2)}px)`
        }
      },
      setWheelDeg (updateDeg, type, time = 1000) {
        if (type === 'end') {
          this.$refs.wheel.style.webkitTransition = `transform ${time}ms cubic-bezier(0.19, 1, 0.22, 1)`
          this.$refs.wheel.style.webkitTransform = `rotate3d(1, 0, 0, ${updateDeg}deg)`
        } else {
          this.$refs.wheel.style.webkitTransition = ''
          this.$refs.wheel.style.webkitTransform = `rotate3d(1, 0, 0, ${updateDeg}deg)`
        }
      },
      setListTransform (translateY = 0, marginTop = 0, type, time = 1000) {
        // 34 => 68
        if (type === 'end') {
          
          this.$refs.list.style.webkitTransition = `transform ${time}ms cubic-bezier(0.19, 1, 0.22, 1)`
          this.$refs.list.style.webkitTransform = `translateY(${((translateY - this.spin.branch * 68)/75*rem).toFixed(2)}px)`
          this.$refs.list.style.marginTop = `${-(marginTop/75*rem).toFixed(2)}px`
          this.$refs.list.setAttribute('scroll', translateY)
        } else {
          
          this.$refs.list.style.webkitTransition = ''
          this.$refs.list.style.webkitTransform = `translateY(${((translateY - this.spin.branch * 68)/75*rem).toFixed(2)}px)`
          this.$refs.list.style.marginTop = `${-(marginTop/75*rem).toFixed(2)}px`
          this.$refs.list.setAttribute('scroll', translateY)
        }
      },
      itemTouchStart (event) {
        let finger = event.changedTouches[0]
        this.finger.startY = finger.pageY
        this.finger.startTime = event.timestamp || Date.now()
        this.finger.transformY = this.$refs.list.getAttribute('scroll')
        event.preventDefault()
      },
      itemTouchMove (event) {
        let finger = event.changedTouches[0]
        this.finger.lastY = finger.pageY
        this.finger.lastTime = event.timestamp || Date.now()
        /* 设置css */
        let move = this.finger.lastY - this.finger.startY
        this.setStyle(move)
        event.preventDefault()
      },
      itemTouchEnd (event) {
        console.log(event);
        console.log('####3');
        let finger = event.changedTouches[0]
        this.finger.lastY = finger.pageY
        this.finger.lastTime = event.timestamp || Date.now()

        

        let move = this.finger.lastY - this.finger.startY
        /* 计算速度 */
        /* 速度计算说明
         * 当时间小于300毫秒 最后的移动距离等于 move + 减速运动距离
         * */
        let time = this.finger.lastTime - this.finger.startTime
        let v = move / time
        /* 减速加速度a */
        let a = 1.8
        /* 设置css */
        if (time <= 300) {
          move = v * a * time
          time = 1000 + time * a
          this.setStyle(move, 'end', time)
        } else {
          this.setStyle(move, 'end')
        }

        console.log('this.toBegin  城市回到定点'+this.toBegin);
        //传值给父组件
        //this.$emit('valueChange', id);
      },
      /* 设置css */
      setStyle (move, type, time) {
        // 34 => 68
        const singleHeight = (68/75*rem).toFixed(2);
        console.log('singleHeight  '+singleHeight);
        const deg = 20
        const singleDeg = deg / singleHeight
        let currentListMove = this.finger.transformY
        let updateMove = move + Number(currentListMove)
        /* 根据滚轮类型 line or cycle 判断 updateMove最大距离 */
        if (this.type === 'line') {
          if (updateMove > 0) {
            updateMove = 0
          }
          if (updateMove < -(this.listData.length - 1) * singleHeight) {
            updateMove = -(this.listData.length - 1) * singleHeight
          }
        }
        let updateDeg = -updateMove * singleDeg
        let spinAim = Math.round(updateDeg / 20)
        let margin = Math.round(updateMove / singleHeight) * singleHeight // 如果不这么写 会导致没有滚动效果
        /* 计算touchEnd移动的整数距离 */
        let endMove = margin
        let endDeg = Math.round(updateDeg / deg) * deg
        if (type === 'end') {
          console.log('测试城市回到上面')
          this.setListTransform(endMove, margin, type, time)
          this.setWheelDeg(endDeg, type, time)
          /* 设置$emit 延迟 */
          setTimeout(() => this.getPickValue(endMove), 100)
        } else {
          console.log('测试城市回到上面type!=end')
          this.setListTransform(updateMove, margin)
          this.setWheelDeg(updateDeg)
        }
        this.updateSpin(spinAim)
      },
      /* 更新spin */
      updateSpin (spinAim) {
        this.spin.start = this.spin.branch * -1 + spinAim
        this.spin.end = this.spin.start + this.spin.branch * 2
      },
      /* 获取spin 数据 */
      getSpinData (index) {
        index = index % this.listData.length
        return this.listData[index >= 0 ? index : index + this.listData.length]
      },
      /* 获取选中值 */
      getPickValue (move) {
        // 34 => 68
        let index =Math.round(Math.abs(move / (68/75*rem).toFixed(2)));
        let pickValue = this.getSpinData(index);
        console.log(index);
        console.log('pickValue  '+pickValue);
        if(this.listName=='provinceName'){
          this.$emit('input', pickValue.provinceName)
          this.$emit('changeVal', [pickValue.id,index])

        }else{
          this.$emit('input', pickValue.areaName)
          this.$emit('changeVal', [pickValue.code,index])
        }
      }
    },
    beforeDestroy () {
      this.$el.removeEventListener('touchstart', this.itemTouchStart)
      this.$el.removeEventListener('touchmove', this.itemTouchMove)
      this.$el.removeEventListener('touchend', this.itemTouchEnd)
    }

  }
</script>
<style lang="less" scoped="">
  html {
    font-family: 'PingFang SC', 'Helvetica Neue', 'Helvetica', 'STHeitiSC-Light', 'Arial', sans-serif;
    line-height: 1.8;
  }

  @color-background: #fff;
  @color-checked: #dedede;
  @color-text-main: #333;
  @color-text-second: #a8a8a8;
  @px30:0.4rem;
  @px20:0.2666666667rem;
  @px10:0.1333333333rem;
  @px1:0.01333333333rem;
  .pd {
    &-select {
      /* 220 => 440 */
      &-item {
        overflow: hidden;
        width: 100%;
        text-align: left;
        height: 440*@px1;
        background: @color-background;
        position: relative;
      }
      &-ul {
        position: relative;
        padding: 0 32*@px1;
      }
      // 98 => 196
      &-line, &-list, &-wheel {
        position: absolute;
        left: 0;
        right: 0;
        top: 196*@px1;
      }
      &-line {
        z-index: 3;
      }
      &-list {
        z-index: 2;
        background: @color-background;
      }
      &-wheel {
        z-index: 1;
        padding: 0;
        margin: 0;
        border:0;
        padding: 0 32*@px1;
        color:#666;
      }
      &-line {
        &:after, &:before {
          position: absolute;
          top: 0;
          content: '';
          display: table;
          background: @color-checked;
          width: 100%;
          height: 4*@px1; // 2=> 4
          -webkit-transform: scaleY(0.5);
          transform: scaleY(0.5);
          -webkit-transform-origin: 0 0;
          transform-origin: 0 0;
        }
        &:before {
          bottom: -2*@px1; // -1 => -2
          top: auto;
        }
      }
      &-line, &-list {
        height: 68*@px1; // 34 => 68
        transform: translate3d(0, 0, 220*@px1); // 110 => 220
      }
      &-list {
        overflow: hidden;
      }
      &-list-item {
        // text-shadow: 0 2px 2px rgba(102, 102, 102, 0.6);  // 1 => 2
        border:0;
        color: #333;
      }
      &-list-item, &-wheel-item {
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        line-height: 68*@px1; // 34 => 68
        font-size: 28*@px1; // 18 => 36
        color: @color-text-main;
        &.hidden {
          visibility: hidden;
          opacity: 0;
        }
      }
      &-wheel {
        transform-style: preserve-3d;
        height: 68*@px1; // 34 => 68
        &-item {
          backface-visibility: hidden;
          position: absolute;
          top: 0;
          width: 100%;
          color: @color-text-second;
          border:0;
        }
      }
    }
  }
</style>
