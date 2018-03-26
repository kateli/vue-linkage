<template>
	<div>
		<div class="custody_openBankAccount">
			<ul>
				<!--<li>
					<div class="label">手机号</div>
					<div class="info">{{bankUserInfo.phoneHide}}</div>
				</li>-->
				<li>
					<div class="label">真实姓名</div>
					<div class="info" v-if="isPublic">{{bankUserInfo.realName}}</div>
					<input type="text" v-else v-model="name" placeholder="请输入真实姓名">
				</li>
				<li>
					<div class="label">身份证号码</div>
					<div class="info" v-if="isPublic">{{bankUserInfo.certNo}}</div>
					<input type="text" v-else v-model="idNum" placeholder="请输入身份证号">
				</li>
				<li>
					<div class="label">银行卡号</div>
					<!-- <div class="info" v-if="isPublic">{{bankUserInfo.cardNoHide}}</div> -->
					<input type="text" placeholder="请输入银行卡号" v-model="bankNum" v-on:input="getBankName()">
				</li>
				<li v-if="showBankName"><!-- class="bankNameLi"-->
					<div class="label">所属银行</div>
					<div class="info" v-bind:data-id="bankNo" v-if="isGetBN&&!ischooseBank" >
						<span>{{bankName}}</span>
					</div>
					<div class="info" v-bind:data-id="bankNo" v-if="!isGetBN" v-on:click="showBankList" v-bind:class="[bankName=='请选择'?'gray jiao':'jiao']">
						{{bankName}}
					</div>
					<div class="info" v-bind:data-id="bankNo" v-if="isGetBN&&ischooseBank" v-on:click="showBankList" v-bind:class="[bankName=='请选择'?'gray jiao':'jiao']">
						{{bankName}}
					</div>
				</li>
				<li>
					<div class="label">开户所在地</div>
					<div class="info" v-bind:class="[address=='请选择银行开户所在地'?'gray jiao':'jiao']" v-on:click="getAddressInfo()">{{address}}</div>
				</li>
			</ul>
			<ul>
				<li>
					<div class="label">设置支付密码</div>
					<input type="password" v-model="pay_pwd" placeholder="请输入8~15位数字和字母">
				</li>
				<li>
					<div class="label">确认支付密码</div>
					<input type="password" v-model="repay_pwd" placeholder="请再次确认支付密码">
				</li>
			</ul> 
			<div class="tip1">此支付密码为知商金融存管版支付密码</div>
			<zui-formError v-model="isError" :error-msg.sync="errorMsg"></zui-formError>
			<button class="nextBtn" type="button" v-bind:class="{'btn_disable':nextDisable}" v-on:click="nextBtn" v-bind:disabled="submitDis">确认开户</button>
			<div class="tip2">点击确认开户，即视为已阅读并同意<a href="../myCenter/bankPrivacy.html">《上海华瑞银行网络借贷资金存管三方协议》</a></div>
			
			<!--省市两级联动弹层-->
			<div class="mask" v-if="areaShow">
				<zui-select-box style="position: fixed;bottom: 0;width: 100%"><!-- -->
					<div class="tit">
						<div class="close" @click="closeArea()">确定</div>
					</div>
					<div style="display: flex;">
						<zui-select-item listName='provinceName' :listData="provinceList" v-model="province" v-on:changeVal="changeValFn"></zui-select-item> 
						<zui-select-item listName='areaName' :listData="cityList" v-model="city" v-on:changeVal="changeValFn2" :toBegin="toBegin"></zui-select-item>
					</div>
				</zui-select-box>
			</div>
			<!--选择银行-->
			<div class="bankListBox" v-if="isShowBL">
				<div class="bankList">
					<div class="word">
						<span v-on:click="isShowBL=false">完成</span>
					</div>
					<dl>
						<dt v-for="(item,index) in allBankList" v-bind:class="[itemIndex==index?'active':'']" v-on:click="chooseBank(index,item.bankName,item.bankNo,item.bankImg)">
							<!-- <img :src="item.bankImg"> -->
							<span>{{item.bankName}}</span>
						</dt>
					</dl>
				</div>
			</div>
			<!--开户失败-->
			<zui-alertNew slot="alertTit" :is-alert.sync="isAlert" alertTit="开户失败" alertBtnWord="确定" :alert-msg.sync="falseMsg" v-on:click="isAlert=!isAlert"></zui-alertNew>
			<!--开户成功-->
			<div class="openAccountRes" v-if="isopenAccountRes"><!--@touchmove.prevent-->
				<div v-bind:class="{'success':openAccountResS}">
					<div class="resIcon"></div>
					<div class="resTit">{{resMsg}}</div>
					<div class="resWord">{{resInfo}}</div>
				</div>
				<a href="../myCenter/user.html">返回我的账户</a>
				<div class="custody_logo"><span>华瑞银行存管</span></div>
			</div>
		</div>
	</div>
</template>
<script>
	import Lib from 'assets/js/Lib';
	import zuiFormError from 'components/zuiFormError';
	import zuiTooltip from 'components/zuiTooltip';
	import zuiSelectItem from 'components/zuiSelectitem.vue';
	import zuiSelectBox from 'components/zuiSlectBox.vue';
	import zuiAlertNew from 'components/zuiAlertNew';
	import './less/custody_openBankAccount.less';
	export default{
		components:{
			zuiTooltip,zuiFormError,zuiSelectItem,zuiSelectBox,zuiAlertNew
		},
		data(){
			return{
				//滑块 start
        		province: 1,
        		city: '',
				//滑块 end
				isPublic:false,//是否是批量开户
				toBegin:false,//城市初始化
				bankUserInfo:{},
				userInfo:{},
				allBankList:{},
				phone:'',
				bankNum:'',
				bankNo:'',
				name:'',
				idNum:'',
				bankName:'',
				bankImg:'',
				address:'请选择银行开户所在地',
				bankPhone:'',
				pay_pwd:'',
				repay_pwd:'',
				provinceCode:'',
				cityCode:'',
				submitDis:false,
				isAlert:false,
				falseMsg:'',
			
				isopenAccountRes:false,//开户结果显示
				openAccountResS:false,//成功开户
				openAccountResA:'',//开户结果a链接地址
				openAccountResAMsg:'',
				resMsg:'',
				resInfo:'',
				isError:false,
				errorMsg:'',
				areaShow:false,
				cityList:[],
				provinceList:[],
				allAddressList:[],
				province:true,
				city:true,
				isGetBN:true,
				isShowBL:false,//手动选择银行列表
				showBankName:false,
				ischooseBank:false,//手动选择银行
			}
			
		},
		created(){
			
			//获取用户信息，未登录跳到登录页
			Lib.M.isLogin(this);
			//获取银行存管用户信息
			this.getBankUserInfo();
			this.getCustody();
		},
		computed:{
			nextDisable:function(){
				if(this.name!=''&&this.idNum!=''&&this.bankNum!=''&&this.isGetBN&&this.cityCode!=''&&this.pay_pwd!=''&&this.repay_pwd!=''){
					this.submitDis=false;
					return false;
				}else{
					this.submitDis=true;
					return true;
				}
				
			}
		},
		mounted(){
			//地区列表
			//this.getAddressInfo();
			this.popstate();
		},
		methods:{
			//是否银行存管
			getCustody:function(){
				var that=this;
				console.log(Lib.M.apiPath);
				Lib.M.ajax({
					'url':'/bankAccount/checkBankAccount',
					'data':{
						"platformId": "1001"
					},
					success:function(res){
						if(res.resultCode==100){
							if(res.data.bankStatus==1){
								window.location.href='../myCenter/user.html';
							}
						}else{
							that.isError=true,
							that.errorMsg=res.resultMsg;
						}
					},
					error:function(){
						console.log('ajaxError');
					}
				})
			},
			//获取详细用户信息
			getBankUserInfo:function(){
				var that=this;
				Lib.M.ajax({
					'url':'/bankAccount/getAccountInfo',
					'data':{
						"platformId": "1001"
					},
					success:function(res){
						console.log('kankan');
						console.log(res);
						if(res.resultCode==100){
							that.bankUserInfo=res.data;
							//初始化数据
							if(that.bankUserInfo.batchFlag==1){
								that.isPublic=true;
								if(that.bankUserInfo.phone){
									that.phone=that.bankUserInfo.phone;
								}
								if(that.bankUserInfo.realName){
									that.name=that.bankUserInfo.realName;
								}
								if(that.bankUserInfo.certNo){
									that.idNum=that.bankUserInfo.certNo;
								}
							}else{
								if(that.bankUserInfo.phone){
									that.phone=that.bankUserInfo.phone;
								}
								if(that.bankUserInfo.realName){
									that.name=that.bankUserInfo.realName;
								}
								if(that.bankUserInfo.certNo){
									that.idNum=that.bankUserInfo.certNo;
								}
								if(that.bankUserInfo.cardNo){
									that.bankNum=that.bankUserInfo.cardNo;
								}
								if(that.bankUserInfo.bankName){
									that.bankName=that.bankUserInfo.bankName;
									that.bankNo=that.bankUserInfo.bankNo;
									that.bankImg=that.bankUserInfo.bankImage;
								}
								if(that.bankUserInfo.city){
									that.provinceCode=that.bankUserInfo.province;
									that.cityCode=that.bankUserInfo.city;
									that.address=that.bankUserInfo.provinceName+' '+that.bankUserInfo.cityName;
								}
							}
							
						}else{
							that.isError=true;
							that.errorMsg=res.resultMsg;
						}
					},
					error:function(){
						console.log('ajaxError');
					}
				})
			},
			//获取省市地区列表
			getAddressInfo:function(){
				var that=this;
				Lib.M.ajax({
					'url':'/bankArea/getAssociateAreas',
					data:{
						"platformId": "1001"
					},
					success:function(res){
						console.log(res);
						console.log(111);
						if(res.resultCode==100){
							that.provinceList=res.data;
							that.cityList=res.data[0].areaInfoVOList;
							console.log(that.provinceList);
							that.allAddressList=res.data;
							that.areaShow=true;
						}
					},
					error:function(){
						console.log('ajaxError');
					}
				})
			},
			changeValFn:function(data){
				var that=this;
				that.toBegin=true;
				console.log('省份测试:');
				console.log(data);
				that.provinceCode=data[0];
				var i=data[1];
				that.cityList=that.allAddressList[i].areaInfoVOList;
				that.cityCode=that.cityList[0].code;
				that.city=that.cityList[0].areaName;
				
			},
			changeValFn2:function(data){
				var that=this;
				that.toBegin=false;
				console.log('城市测试:');
				console.log(data);
				that.cityCode=data[0];
				
			},
			//关闭弹出层
			closeArea:function(){
				this.areaShow=false;
				this.address=this.province+this.city;
			},
			//根据卡号获得银行信息
			getBankName:function(){
				var that=this;
				if(that.bankNum.length>=6){
					that.showBankName=true;
					Lib.M.ajax({
						'url':'/bankCard/matchBank',
						'data':{
							"cardNo": that.bankNum,
							"channelType":"1", //0兴业 1富友 2.PC
							"platformId":"1001"
						},
						success:function(res){
							console.log(res);
							if(res.resultCode==100){
								if(res.data!=null){
									that.isGetBN=true;
									that.ischooseBank=false;
									that.bankName=res.data.bankName;
									that.bankImg=res.data.bankImg;
									that.bankNo=res.data.bankNo;
								}else{
									that.isGetBN=false;
									that.bankName='请选择';
									// that.isError=true;
									// that.errorMsg='暂不支持该银行';
								}						
							}else{
								that.bankName='';
								that.bankImg='';

							}
						},
						error:function(){
							console.log('ajaxError');
						}
					})
				}
			},
			//输入卡号没有匹配到银行，显示银行列表手动选择
			showBankList:function(){
				var that=this;
				console.log("喵喵喵？？");
				Lib.M.ajax({
					'url':'/bankCard/getAllBankByOpenAccount',
					success:function(res){
						
						if(res.resultCode==100){
							that.allBankList=res.data;							
						}else{
							that.isError=true;
							that.errorMsg=res.resultMsg;
						}
					},
					error:function(){
						console.log('ajaxError');
					}
				})
				that.isShowBL=true;
			},
			//点击银行卡列表中的银行
			chooseBank:function(index,bankName,bankNo,bankImg){
				var that=this;
				that.bankName=bankName;
				that.bankNo=bankNo;
				that.isGetBN=true;
				that.bankImg=bankImg;
				that.ischooseBank=true;
				that.itemIndex=index;
			},
			//银行存管开户
			nextBtn:function(){
				var that=this;
				var pwd_reg = /^(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{8,15}$/;
				//验证身份证号码
				var testIdNum = /\d{17}[\d|x]|\d{15}/;
				if(testIdNum.test(that.idNum)==false){
					that.isError=true;
					that.errorMsg='身份证号码格式错误';
					return false;
				}
				if ((that.bankNum).length<15 ){
					that.isError=true;
					that.errorMsg='请输入正确银行卡号';
					return false;
				}
				if(that.isGetBN==false){
					that.isError=true;
					that.errorMsg='请选择银行';
					return false;
				}

				if(that.pay_pwd!=that.repay_pwd){
					that.isError=true;
					that.errorMsg='两次支付密码不相同';
					return false;
				}
				if(!pwd_reg.test(that.pay_pwd)){
					that.isError=true;
					that.errorMsg='密码由长度在8-15位英文、数字组合';
					return false;
				}
				that.submitDis=true;
				that.errorMsg='';
				console.log(that.phone);
				Lib.M.ajax({
					'url':'/bankAccount/createAccount',
					'data':{
						"bankName": that.bankName,
						"bankNo":that.bankNo,
						"cardNo": that.bankNum,
						"certNo": that.idNum,
						"city": that.cityCode,
						"lpassword": that.pay_pwd,
						"phone": that.phone,
						"platformId": "1001",
						"province": that.provinceCode,
						"realName": that.name
					},
					success:function(res){
						console.log(res);
						
						if(res.resultCode==100){
							console.log('开户成功');
							window.location.hash='openAccountRes';
							that.isopenAccountRes=true;
							that.openAccountResS=true;
							that.resMsg='开户成功';
							that.resInfo='成功开通银行存管';
							that.openAccountResA='../home/index.html';
							that.openAccountResAMsg="返回首页";
						}else{
							console.log('开户失败');
							that.isAlert=true;
							that.isError=true;
							that.falseMsg=res.resultMsg;
							that.submitDis=false;
						}
					},
					error:function(){
						console.log('开通银行存管 Ajax失败');
					}
				})
				
			},
			//监听点击返回按钮
			popstate:function(){
				var that=this;
				window.addEventListener("popstate", function() { 
					console.log('ei???');
					that.isShowBL=false;
					that.isopenAccountRes=false;
					document.getElementsByTagName('body')[0].style.height='';
					document.getElementsByTagName('body')[0].style.overflow='';
				});
			},
			//返回
			back:function(){
				window.location.href='../myCenter/user.html'
			}
		}
	}
</script>
