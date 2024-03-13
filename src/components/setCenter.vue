<script lang="ts" setup>
import { useStore } from "vuex";
import IconDropOut from "@/views/calendar/icon/icon-drop-out.vue";
import { UserControllerService } from "../../generated";
import message from "@arco-design/web-vue/es/message";
import { useRouter } from "vue-router";
import IconAccountSetting from "@/views/calendar/icon/icon-account-setting.vue";

const store = useStore();
const router = useRouter();

// 注销
const doLogout = async () => {
  const res = await UserControllerService.userLogoutUsingPost();
  // 注销成功跳转页面
  if (res.code === 0) {
    await router.push({
      path: "/",
      replace: true,
    });
    location.reload(); // 刷新页面
  } else {
    message.error("注销失败:" + res.message);
  }
};
// 账号设置
const goCenter = () => {
  router.push({
    path: "/personal/center",
  });
};

// 个人中心
const goHome = () => {
  router.push({
    path: "/personal/homepage",
  });
};
</script>

<template>
  <div
    style="
      padding: 16px;
      width: 296px;
      background-color: var(--color-bg-popup);
      border-radius: 20px;
      box-shadow: 0 2px 8px 0 rgba(0, 0, 0, 0.15);
    "
  >
    <a-space :size="10" direction="vertical" style="color: black">
      <div class="avatar-wrapper">
        <img
          v-if="store.state.user?.loginUser?.userAvatar"
          :src="store.state.user?.loginUser?.userAvatar"
          alt=""
          class="round-avatar"
          @click="goHome"
        />
        <img
          v-else
          alt="avatar"
          class="round-avatar"
          src="https://himg.bdimg.com/sys/portraitn/item/03cde88aa5e69cabe59682e58fa3e59abce7b396df8f"
        />
        <div
          style="
            display: flex;
            flex-direction: column;
            align-items: flex-start;
            width: 180px;
          "
        >
          <span
            v-if="store.state.user?.loginUser?.userName"
            class="username"
            @click="goHome"
            >{{ store.state.user?.loginUser?.userName }}</span
          >
          <span v-else class="username" @click="goHome">{{
            store.state.user?.loginUser?.userAccount
          }}</span>
          <a-progress :percent="0.2" :show-text="false" />
        </div>
      </div>
      <div class="container" @click="doLogout">
        <icon-drop-out
          color="#969696"
          size="20"
          style="
            margin-left: 10px;
            margin-right: 15px;
            stroke: currentColor;
            stroke-width: 8px;
          "
        />
        <span style="color: #0000008c; margin-top: 3px">退出</span>
      </div>
    </a-space>
  </div>
</template>

<style scoped>
.avatar-wrapper {
  display: flex; /* 使用 Flex 布局 */
  align-items: center; /* 垂直居中 */
  justify-content: flex-start;
}

.round-avatar {
  border-radius: 50%; /* 将图片设为圆形 */
  width: 66px; /* 图片宽度 */
  height: 66px; /* 图片高度 */
  margin-right: 15px; /* 图片右侧留出一些间距 */
  cursor: pointer;
}

.username {
  font-weight: bold; /* 设置字体加粗 */
  font-size: 20px; /* 设置字体大小为16像素 */
  cursor: pointer;
}

.grid-item {
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 10px;
  background-color: #0000000a;
  width: 86px;
  height: 86px;
}

.grid-item:hover {
  background-color: #f0f0f0;
}

.container {
  height: 41px;
  border-radius: 5px;
  display: flex;
  align-items: center;
  cursor: pointer;
}

.container:hover {
  background-color: #f7f7f8;
}
</style>
