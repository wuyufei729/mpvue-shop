<template>
	<div class="login">
		<div class="top">
            <image class="logoBox" :src="bgImg"></image>
            <h1>电信诈骗取证系统</h1>
            <span class="circle circle1"></span>
            <span class="circle circle2"></span>
            <span class="circle circle3"></span>
            <span class="circle circle4"></span>
            <!-- <span class="circle"></span>
            <span class="circle"></span>
            <span class="circle"></span> -->
		</div>
        <div class="bottom">
            <div class="input-phone">
                <p>
                    <input type="number" placeholder="手机号"/>
                </p>
                <div class="get-code">
                    <label>获取验证码</label>
                </div>
            </div>
            <div class="input-code" ref="inputCode">
                <span>{{codeList!=null&&codeList.length>0?codeList[0]:''}}</span>
                <span>{{codeList!=null&&codeList.length>1?codeList[1]:''}}</span>
                <span>{{codeList!=null&&codeList.length>2?codeList[2]:''}}</span>
                <span>{{codeList!=null&&codeList.length>3?codeList[3]:''}}</span>
                <input type="text" class="plateInput" v-model="code" >
            </div>

        </div>
		<!-- <button
			class="login-btn"
			open-type="getUserInfo"
			lang="zh_CN"
			@getuserinfo="doLogin"
		>微信登录2</button> -->
	</div>
</template>

<script>
import { host } from "../../utils";
import bgImg from '../../../static/images/logo_gh.png'
var qcloud = require("wafer2-client-sdk/index.js");
export default {
	created() { },
	mounted() {
		console.log(host);

		qcloud.setLoginUrl(host + "/login");
		// wx.login({success: function(res) {
		//   debugger
		// }})
	},
	data() {
		return {
            bgImg:bgImg,
            focusIndex: 0,
            code: null,
            codeList: null,
            codeMaxLength: 4,
        };
    },
    watch: {
        code(newName, oldName) {
            this.codeList = newName;
            if(this.codeList.length == this.codeMaxLength){
                this.handlerLogin();
            }
        }
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
                wx.navigateTo({
                    url: "/pages/index/main"
                });
            },3000)

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
		}
	},
	computed: {}
};

</script>
<style lang='scss' scoped>
  @import "./style";

</style>
