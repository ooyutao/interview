<template>
  <div class="page-layout">
    <header-top></header-top>
    <div class="container">
      <side-bar class="left"></side-bar>
      <div class="content">
        <router-view></router-view>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import HeaderTop from '/@/views/Layout/HeaderTop.vue'
import SideBar from '/@/views/Layout/SideBar.vue'
import { provide } from 'vue'

const setTest = (v) => {
  alert(v)
}
provide('test', setTest)
</script>
<style lang="scss" scoped>
.page-layout {
  height: 100vh;
  overflow: hidden;
}
.container {
  height: calc(100% - 50px);
  display: flex;
  background: #e9e9e9;
  .left {
    width: 240px;
  }
  .content {
    flex: 1;
    margin: 10px;
    background: #fff;
    box-sizing: border-box;
    width: 100%;
    height: calc(100% - 20px);
    overflow: auto;
  }
}
</style>
