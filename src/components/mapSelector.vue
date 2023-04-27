<!--
 * @Description: 百度地图窗口
 * @Author: LyKai
 * @Date: 2023-04-27 21:24:45
 * @LastEditors: LyKai
 * @LastEditTime: 2023-04-28 00:01:51
-->
<script setup>
import { computed, onMounted, ref, watch } from 'vue';
const props = defineProps(['initData', 'isEdit']);
const emits = defineEmits(['returnData']);

//是否是区域数据
const isAreaData = computed(() => {
  return Array.isArray(props.initData);
});

const BDMapObj = ref(null);
// 储存已经转换过的坐标
const bPointList = ref([]);
watch(bPointList, list => {
  console.log('list', list);
  drawArea(list);
});
//初始化地图
const initMap = () => {
  props.initData;
  BDMapObj.value = new BMapGL.Map('container');
  BDMapObj.value.enableScrollWheelZoom(true);

  // 转换坐标
  (isAreaData.value ? props.initData : [props.initData]).forEach(p => {
    const [lat, lng] = p.split(',');
    bPointList.value.push(new BMapGL.Point(lat, lng));
  });

  if (isAreaData.value) {
    drawArea(bPointList.value);
  } else {
    drawPoint(bPointList.value[0]);
  }
  const { center, zoom } = BDMapObj.value.getViewport(eval(bPointList.value));
  BDMapObj.value.centerAndZoom(center, zoom);

  if (props.isEdit) {
    BDMapObj.value.addEventListener('click', ev => {
      const { lat, lng } = ev.latlng;
      let bdpoint = new BMapGL.Point(lat, lng);
      if (isAreaData.value) {
        bPointList.value.push(bdpoint);
        console.log('initMap -> bPointList.value', bPointList.value);
      } else {
        BDMapObj.value.clearOverlays();
        drawPoint(ev.latlng);
      }
    });
  }
};

// 绘制坐标点
const drawPoint = bdPoint => {
  var marker = new BMapGL.Marker(bdPoint);
  BDMapObj.value.addOverlay(marker);
};

//绘制区域
const drawArea = bdPoint => {
  const polygon = new BMapGL.Polygon(bdPoint, {
    strokeColor: 'blue',
    strokeWeight: 2,
    strokeOpacity: 0.5
  });
  polygon.enableMassClear();
  BDMapObj.value.addOverlay(polygon);
};
// 返回点坐标
const saveData = () => {
  let points = [];
  bPointList.value.forEach(e => {
    const { lat, lng } = e;
    points.push(`${lng},${lat}`);
  });
  console.log(points);
  emits('returnData', points);
};
onMounted(() => {
  initMap();
});
</script>

<template>
  <div class="map-selector">
    <div v-if="props.isEdit" class="btn" @click="BDMapObj.clearOverlays">
      开始绘制
    </div>
    <div v-if="props.isEdit" class="btn" @click="BDMapObj.clearOverlays">清空</div>
    <div v-if="props.isEdit" class="btn" @click="saveData">保存</div>
    <div class="bamap" id="container"></div>
  </div>
</template>

<style scoped>
.map-selector {
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.bamap {
  width: 800px;
  height: 800px;
}

.btn {
  width: 100px;
  height: 30px;
  border: 1px solid skyblue;
}
</style>
