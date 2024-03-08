<template>
  <div class="container">
    <div class="footer">
      Powered by {{ author }} @
      <el-link type="primary" href="https://github.com/yangjian102621/chatgpt-plus" target="_blank">{{ privacy }}
      </el-link>
      <el-link type="primary" href="https://github.com/yangjian102621/chatgpt-plus" target="_blank">{{ policies }}
      </el-link>
    </div>
  </div>
</template>
<script setup>

import {ref} from "vue";

const title = ref(process.env.VUE_APP_TITLE)
const privacy = ref('隐私政策')
const policies = ref('用户协议')
const author = ref('注册或登录即表示您已阅读并同意隐私政策和用户协议')
</script>

<style scoped lang="stylus">
.container {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
  display flex;
  justify-content center

  .footer {
    max-width 400px;
    text-align center;
    font-size 14px;
    padding 20px;
    width 100%
  }
}

</style>