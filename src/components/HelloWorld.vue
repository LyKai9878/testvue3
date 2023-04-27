<!--
 * @Description: 
 * @Author: LyKai
 * @Date: 2023-04-15 19:35:21
 * @LastEditors: LyKai
 * @LastEditTime: 2023-04-28 00:01:01
-->
<script setup>
import { ref } from 'vue';
import MapSelector from './mapSelector.vue';
const isShowMap = ref(false);
const isEdit = ref(true);
// const isEdit = ref(false);
const mapInitData = ref(null);
const openMap = () => {
  // mapInitData.value = '138.223463,36.686654';
  mapInitData.value = [
    '121.930069,30.889105',
    '121.96169,30.89208',
    '121.960827,30.914762',
    '121.931507,30.91377'
  ];
  isShowMap.value = true;
};
const getPoints = points => {
};
</script>

<template>
  <div class="helloword">
    <div class="btn" @click="openMap">查看点</div>
    <div class="btn" @click="openMap">编辑点</div>
    <div class="btn" @click="openMap">查看范围</div>
    <div class="btn" @click="openMap">编辑范围</div>

    <MapSelector
      v-if="isShowMap"
      :initData="mapInitData"
      :isEdit="isEdit"
      @returnData="getPoints"
    />
  </div>
</template>

<style scoped>
.helloword {
  width: 100%;
  height: 100%;
}
.btn {
  width: 100px;
  height: 30px;
  border: 1px solid skyblue;
}
</style>
