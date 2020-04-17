<template>
	<div class="login">
		<div class="top">
            <image class="logoBox" :src="bgImg"></image>
            <h1>电信诈骗取证系统</h1>
            <span class="circle circle1"></span>
            <span class="circle circle2"></span>
            <span class="circle circle3"></span>
            <span class="circle circle4"></span>
		</div>
        <div class="bottom">
            <div class="input-phone">
                <p>
                    <input type="number" placeholder="手机号"/>
                </p>
                <div class="get-code">
                    <label v-if="timerStatus == 0" @click="getPhoneNum()">获取验证码 </label>
                    <label v-if="timerStatus == 1">{{seconds}} (s)</label>
                    <label v-if="timerStatus == 2" @click="getPhoneNum()">重新发送</label>
                    
                </div>
            </div>
            <div class="input-code" ref="inputCode" v-if="codeMaxLength != null">
                <span v-for="(item,i) in codeMaxLength" :key="i">{{codeList!=null&&codeList.length>i?codeList[i]:''}}</span>
                <!-- <span>{{codeList!=null&&codeList.length>0?codeList[0]:''}}</span>
                <span>{{codeList!=null&&codeList.length>1?codeList[1]:''}}</span>
                <span>{{codeList!=null&&codeList.length>2?codeList[2]:''}}</span>
                <span>{{codeList!=null&&codeList.length>3?codeList[3]:''}}</span> -->
                <input type="text" class="plateInput" v-model="codeList" >
            </div>
            <button class="login-btn" open-type="getUserInfo" lang="zh_CN" @getuserinfo="handlerLogin">登录</button>
        </div>
	</div>
</template>

<script>
import { host } from "../../utils";
import bgImg from '../../../static/images/logo_gh.png'
var qcloud = require("wafer2-client-sdk/index.js");
export default {
	data() {
		return {
            bgImg:bgImg,
            codeList: null,//输入的验证码
            codeMaxLength: 4,//验证码长度

            timerStatus: 0,
            defaultSeconds: 10,
            seconds: 0,
            postCodeTimer: null,
        };
    },
    watch: {
        codeList(newName, oldName) {
            //当输入位数到达设定时候
            if(this.codeList != null && this.codeList.length > this.codeMaxLength){
                this.codeList = oldName;
                //this.handlerLogin();
            }
        }
    },
    
    mounted() {
		console.log(host);

		//qcloud.setLoginUrl(host + "/login");
		// wx.login({success: function(res) {
		//   debugger
        // }})
	},
	
	methods: {

        
        handlerLogin(){
             
            wx.showLoading({
				title: "登录中...", //提示的内容,
				mask: true, //显示透明蒙层，防止触摸穿透,
				success: res => { }
            });
            setTimeout(()=>{
                wx.hideLoading();
                wx.switchTab({
                    url: "../index/main"
                });
            },1000)

        },

		doLogin() {
			wx.showLoading({
				title: "登录中...", //提示的内容,
				mask: true, //显示透明蒙层，防止触摸穿透,
				success: res => { }
			});
			const session = qcloud.Session.get();
			if (session) {
				// 第二次登录
				// 或者本地已经有登录态
				// 可使用本函数更新登录态
				qcloud.loginWithCode({
					success: res => {
						// this.setData({ userInfo: res, logged: true });
						wx.setStorageSync("key", "value");
					},
					fail: err => {
						console.error(err);
					}
				});
			} else {
				// 首次登录
				qcloud.login({
					success: res => {
						console.log(res);

						wx.hideLoading();
						wx.setStorageSync("userInfo", res);
						var openId = res.openId;
						wx.setStorageSync("openId", openId);
						wx.navigateBack({});
					},
					fail: err => {
						console.log(err);
						wx.hideLoading();
						wx.navigateBack({});
					}
				});
			}
        },
        
        /**
         * 点击获取验证码
         */
        getPhoneNum(){
            this.timerStatus = 1;
            this.initTimer();
        },
        
        /**
         * 验证码倒计时
         */
        initTimer() {
            this.seconds = this.defaultSeconds;
            this.postCodeTimer = setInterval(()=>{
                if(this.seconds == 0){
                    clearInterval(this.postCodeTimer);
                    this.timerStatus = 2;
                }
                this.seconds -= 1;
            },1000)
        },

	},
};

</script>
<style lang='scss' scoped>
  @import "./style";

</style>
