<template>
  <div class="main">
    <div class="title">
      <a class="active" href="/login">登录</a>
      <span>·</span>
      <a href="/register">注册</a>
    </div>

    <div class="sign-up-container">
      <el-form ref="userForm" :model="user">
        <el-form-item
          class="input-prepend restyle"
          prop="mobile"
          :rules="[
            { required: true, message: '请输入手机号码', trigger: 'blur' },
            { validator: checkPhone, trigger: 'blur' },
          ]"
        >
          <div>
            <el-input type="text" placeholder="手机号" v-model="user.mobile" />
            <i class="iconfont icon-phone" />
          </div>
        </el-form-item>

        <el-form-item
          class="input-prepend"
          prop="password"
          :rules="[{ required: true, message: '请输入密码', trigger: 'blur' }]"
        >
          <div>
            <el-input
              type="password"
              placeholder="密码"
              v-model="user.password"
            />
            <i class="iconfont icon-password" />
          </div>
        </el-form-item>

        <div class="btn">
          <input
            type="button"
            class="sign-in-button"
            value="登录"
            @click="submitLogin()"
          />
        </div>
      </el-form>
      <!-- 更多登录方式 -->
      <div class="more-sign">
        <h6>社交帐号登录</h6>
        <ul>
          <li>
            <a
              id="weixin"
              class="weixin"
              target="_blank"
              href="http://qy.free.idcfengye.com/api/ucenter/weixinLogin/login"
              ><i class="iconfont icon-weixin"
            /></a>
          </li>
          <li>
            <a id="qq" class="qq" target="_blank" href="#"
              ><i class="iconfont icon-qq"
            /></a>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import "~/assets/css/sign.css";
import "~/assets/css/iconfont.css";
import cookie from "js-cookie";
import loginApi from "@/api/login";

export default {
  layout: "sign",
  data() {
    return {
      user: {
        mobile: "",
        password: "",
      },
      loginInfo: {},
    };
  },
  created() {},
  methods: {
    submitLogin() {
      loginApi.login(this.user).then((response) => {
        if (response.data.success) {
          console.log("开始输出")
          console.log(response);
          //把token存在cookie中、也可以放在localStorage中
          cookie.set("guli_token", response.data.data.token, {
            domain: "localhost",
          });
          //那cookie里面的token换取用户信息
          loginApi.getUcenterByToken().then((response) => {
            this.loginInfo = response.data.data.ucenterMember;
            //将用户信息记录cookie
            cookie.set("guli_ucenter", JSON.stringify(this.loginInfo), { domain: "localhost" });
            console.log("loginInfo", this.loginInfo);
            console.log("guli_ucenter", cookie.get("guli_ucenter"));
            //跳转页面
            window.location.href = "/";
          });
        }
      });
    },
  },
};
</script>