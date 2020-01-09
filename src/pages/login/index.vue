<template>
  <div class="login">
    <div class="logo">

    </div>
    <button class="login-btn" open-type="getUserInfo" lang="zh_CN" @getuserinfo="doLogin">微信登录</button>
  </div>
</template>

<script>
  import {
    host
  } from "../../utils";
  var qcloud = require("wafer2-client-sdk/index.js");
  export default {
    created() {},
    mounted() {
      console.log(host);

      // qcloud.setLoginUrl(host + "/login");
    },
    data() {
      return {};
    },
    components: {},
    methods: {
      doLogin() {
        wx.showLoading({
          title: "登录中...", //提示的内容,
          mask: true, //显示透明蒙层，防止触摸穿透,
          success: res => {}
        });
        // const session = qcloud.Session.get();
        // if (session) {
        //   // 第二次登录
        //   // 或者本地已经有登录态
        //   // 可使用本函数更新登录态
        //   qcloud.loginWithCode({
        //     success: res => {
        //       // this.setData({ userInfo: res, logged: true });
        //       wx.setStorageSync("key", "value");
        //     },
        //     fail: err => {
        //       console.error(err);
        //     }
        //   });
        // } else {
        //   // 首次登录
        //   qcloud.login({
        //     success: res => {
        //       console.log(res);

        //       wx.hideLoading();
        //       wx.setStorageSync("userInfo", res);
        //       var openId = res.openId;
        //       wx.setStorageSync("openId", openId);
        //       wx.navigateBack({});
        //     },
        //     fail: err => {
        //       console.log(err);
        //       wx.hideLoading();
        //       wx.navigateBack({});
        //     }
        //   });
        // }
        wx.login({
          success: res => {
            if (res.code) {
              //发起网络请求
              wx.request({
                url: 'https://api.weixin.qq.com/sns/jscode2session',
                data: {
                  "appid": "wx3748cb59f060deb9",
                  "secret": "32e60bdf053d00c37a0768472b04c31e",
                  "js_code": res.code,
                  "grant_type": "authorization_code"
                },
                success: function (data) {
                  var openid = data.data.openid;
                  wx.setStorageSync("openid", openid);
                  wx.getUserInfo({
                    withCredentials: true,
                    lang: 'zh_CN',
                    success: function (res) {
                      var userInfo = res.userInfo;
                      wx.setStorageSync("userInfo", userInfo);
                      wx.hideLoading();
                      wx.navigateBack({});
                    }
                  })
                },
                fail: (res) => {
                  console.log('登录失败！' + res.errMsg)
                },
              })
            } else {
              console.log('登录失败！' + res.errMsg)
            }
          },
          fail: () => {},
          complete: () => {}
      });
      }
    },
    computed: {}
  };

</script>
<style lang='scss' scoped>
  @import "./style";

</style>
