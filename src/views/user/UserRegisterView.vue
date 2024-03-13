<template>
  <div id="userLoginView">
    <h2 style="margin-bottom: 16px">用户注册</h2>
    <a-form
      :model="form"
      auto-label-width
      label-align="left"
      style="max-width: 480px; margin: 0 auto"
      @submit="handleSubmit"
    >
      <a-form-item
        field="userAccount"
        label="账号"
        tooltip="账号不少于4位"
        :rules="rulesUserAccount"
      >
        <a-input v-model="form.userAccount" placeholder="请输入账号" />
      </a-form-item>
      <a-form-item
        field="userPassword"
        label="密码"
        tooltip="密码不少于 8 位"
        :rules="rulesCheckPassword"
      >
        <a-input-password
          v-model="form.userPassword"
          placeholder="请输入密码"
        />
      </a-form-item>
      <a-form-item
        field="userPassword"
        label="密码"
        tooltip="密码不少于 8 位"
        :rules="rulesUserPassword"
      >
        <a-input-password
          v-model="form.checkPassword"
          placeholder="请确认密码"
        />
      </a-form-item>
      <a-form-item style="display: flex">
        <a-button
          size="large"
          type="dashed"
          style="width: 168px; border-radius: 10px 0 0 10px"
          @click="goBack"
        >
          返回
        </a-button>
        <a-button
          @click="handleSubmit"
          size="large"
          type="primary"
          html-type="submit"
          style="width: 168px; border-radius: 0 10px 10px 0"
        >
          注册
        </a-button>
      </a-form-item>
    </a-form>
  </div>
</template>

<script lang="ts" setup>
import { reactive, ref } from "vue";
import message from "@arco-design/web-vue/es/message";
import { useRouter } from "vue-router";
import { useStore } from "vuex";
import { UserControllerService, UserRegisterRequest } from "../../../generated";
const goBack = () => {
  router.back();
};
/**
 * 表单信息
 */
const form = reactive({
  userAccount: "",
  userPassword: "",
  checkPassword: "",
} as UserRegisterRequest);

const router = useRouter();
const store = useStore();
const Account_Verification = ref(false);
const Password_Verification = ref(false);
const CheckPassword_Verification = ref(false);
const rulesUserAccount = [
  {
    validator: (value: string, cb: (arg0: string) => void) => {
      return new Promise<void>((resolve) => {
        window.setTimeout(() => {
          if (value == null) {
            cb("账号不可以为空！");
          } else if (value.trim() == "") {
            cb("不能包含空格！");
          } else if (
            !/(?=.*\d)(?=.*[^a-zA-Z\d])/.test(value) &&
            !/(?=.*[a-zA-Z])(?=.*\d)/.test(value) &&
            !/(?=.*[a-zA-Z])(?=.*[^a-zA-Z\d])/.test(value)
          ) {
            cb("账号必须包含字母、数字、符号中至少两种类型！");
          } else if (value.length < 5 || value.length > 16) {
            cb("账号长度应为5-16个字符！");
          } else if (/([a-zA-Z0-9])\1{5,}/.test(value)) {
            cb("账号不可以包含连续重复的6位及以上的字母或数字！");
          } else {
            Account_Verification.value = true;
          }
          resolve();
        }, 1000);
      });
    },
  },
];
const userPassword = ref();

const rulesUserPassword = [
  {
    validator: (value: string, cb: (arg0: string) => void) => {
      return new Promise<void>((resolve) => {
        window.setTimeout(() => {
          userPassword.value = value;
          if (value == null) {
            cb("密码不可以为空！");
          } else if (value.trim() == "") {
            cb("不能包含空格！");
          } else if (value.length < 8 || value.length > 16) {
            cb("密码长度应为8-16个字符！");
          } else if (
            !/(?=.*\d)(?=.*[^a-zA-Z\d])/.test(value) &&
            !/(?=.*[a-zA-Z])(?=.*\d)/.test(value) &&
            !/(?=.*[a-zA-Z])(?=.*[^a-zA-Z\d])/.test(value)
          ) {
            cb("密码必须包含字母、数字、符号中至少两种类型！");
          } else if (/([a-zA-Z0-9])\1{5,}/.test(value)) {
            cb("密码不可以包含连续重复的6位及以上的字母或数字！");
          } else {
            Password_Verification.value = true;
          }
          resolve();
        }, 1000);
      });
    },
  },
];
const checkPassword = ref();

const rulesCheckPassword = [
  {
    validator: (value: string, cb: (arg0: string) => void) => {
      return new Promise<void>((resolve) => {
        window.setTimeout(() => {
          if (value == null) {
            cb("确认密码不可以为空！");
          } else if (value.trim() == "") {
            cb("不能包含空格！");
          } else if (value.length < 8 || value.length > 16) {
            cb("确认密码长度应为8-16个字符！");
          } else if (
            !/(?=.*\d)(?=.*[^a-zA-Z\d])/.test(value) &&
            !/(?=.*[a-zA-Z])(?=.*\d)/.test(value) &&
            !/(?=.*[a-zA-Z])(?=.*[^a-zA-Z\d])/.test(value)
          ) {
            cb("确认密码必须包含字母、数字、符号中至少两种类型！");
          } else if (/([a-zA-Z0-9])\1{5,}/.test(value)) {
            cb("确认密码不可以包含连续重复的6位及以上的字母或数字！");
          } else {
            CheckPassword_Verification.value = true;
          }
          resolve();
        }, 1000);
      });
    },
  },
];

/**
 * 提交表单
 * @param data
 */
const handleSubmit = async () => {
  if (
    Password_Verification.value == false &&
    CheckPassword_Verification.value == false &&
    Account_Verification.value == false
  ) {
    return;
  }
  const res = await UserControllerService.userRegisterUsingPost(form);
  if (res.code === 0) {
    message.success("注册成功");
    await router.push({
      path: "/user/login",
      replace: true,
    });
  } else {
    message.error("注册失败 " + res.message);
  }
};
</script>
