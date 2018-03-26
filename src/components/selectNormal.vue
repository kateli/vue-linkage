<template>
	<div class="selectNormal"><!-- :data-value="selectValue"-->

		<div class="selectParent" v-on:click="selectClick()">
			<div class="placeholder" v-if="placeholder!=''">{{placeholder}}</div>
			<div v-if="selectMsg!=''">{{selectMsg}}</div>
			<div class="jiao"></div>
			<!-- <div class="conBox">
				<slot></slot>
				<slot name="alertTit" v-if="alertTit!=''">
					<div class="tit"><span>{{alertTit}}</span></div>
					<div class="tip">{{alertMsg}}</div>
				</slot>
				<slot name="onlyMsg" v-if="alertTit==''&&alertMsg!=''">
					<div class="tip onlyMsg" v-html="alertMsg"></div>
				</slot>
				<a href="javascript:void(0)" v-on:click="btnClick()" class="alertBtn">{{alertBtnWord}}</a>
			</div> -->
		</div>
		<div class="bg" v-show="isSelectItem" v-on:click="selectItemShowFn"><!--v-on:click="isSelectItem=!isSelectItem"-->
			<div class="selectItemBox">
				<div v-for="item in selectItemArr"  v-on:click="selectItemFn(item.id,item.name)" class="selectItem" :data-value="item.id">{{item.name}}</div>
			</div>
			
		</div>
	</div>
</template>
<script>
export default {
	data(){
		return {
			selectMsg:''
		}
	},
	props: {
		isSelectItem: {
			default: false
		},
		placeholder: {
			default: ''
		},
		selectItemArr:{
			default: function(){
				return []
			}
		}
	},
	methods:{
		selectClick:function(){//点击下拉，出现选择弹层
			this.$emit("click");
		},
		selectItemFn:function(id,name){//点击下拉框选项，传递选择值并隐藏下拉框
			this.$emit("changeSelectItem",id);
			console.log('子组件 id')
			console.log(id);
			this.selectMsg=name;
			console.log(this.placeholder);
		},
		selectItemShowFn:function(){//点击遮罩，隐藏下拉选择框
			this.$emit("isSelectItemShow");
		}

	}
}
</script>
<style lang='less' scoped>
@import '../assets.less';
.selectNormal{
	display: inline-block;
	
	position: relative;
	.selectParent{
		.placeholder{color:#C7C7CD; }
		.jiao{
			.afterJiao;
		}
	}
	.bg{
		position: fixed;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		background-color: rgba(0,0,0,0.5);
		z-index: 999;
		text-align: center;
		&:after{
			content: '';
			display: inline-block;
			vertical-align: middle;
			height: 100%;
			width: 0;
		}
		.selectItemBox{
			text-align: left;
			background-color: @white;
			// text-align: center;
			width: 684*@px1;
			// height: 464*@px1;
			border-radius: 4*@px1;
			display: inline-block;
			vertical-align: middle;
			margin: auto;
			.selectItem{
				font-size: 24*@px1;
				color: @color333;
				border-bottom:1px solid @colorddd;
				line-height: 2;
				padding: 20*@px1;
				&:last-of-type{
					border: none;
				}
				&:first-of-type{
					border-top: none;
				}
			}
		}
	}
	
}
</style>


