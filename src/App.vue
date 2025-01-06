<template>
  <div :style="{ position: 'relative' }">
    <div id="cesiumContainer"></div>
    <div :style="{position: 'absolute', top: '10px', left: '10px', zIndex: '1000'}">
      <button :style="{ marginRight: '10px' }"  @click="showOffice">只顯示Office</button>
      <button :style="{ marginRight: '10px' }"  @click="colorEveryArea">按照各行政區塗顏色</button>
      <button @click="backToInitial">回復原狀</button>
    </div>
  </div>
</template>
<script setup>
import { Cartesian3, Math as CesiumMath, Terrain, Viewer, createOsmBuildingsAsync, Cesium3DTileStyle } from "cesium";
import { onMounted } from "vue";
let buildingTileset
onMounted(async () => {
  const viewer = new Viewer("cesiumContainer", {
    terrain: Terrain.fromWorldTerrain(),
  });
  buildingTileset = await createOsmBuildingsAsync()
  viewer.scene.primitives.add(buildingTileset);
  viewer.scene.camera.setView({
    destination: Cartesian3.fromDegrees(121.5658, 25.02323, 550),
    orientation: {
      heading: CesiumMath.toRadians(0.0), //圍繞 Y軸（垂直方向） 旋轉
      pitch: CesiumMath.toRadians(-15.0), //圍繞 X軸（水平方向） 旋轉
    },
  });
})
const showOffice = () => {
  buildingTileset.style = new Cesium3DTileStyle({
    show: "${feature['building']} === 'office'",
  });
}

const backToInitial = () => {
  buildingTileset.style = new Cesium3DTileStyle({
    show: true,
  });
}

const colorEveryArea = () => {
  const districtColor = {
    北投區: '#FF0000', // 紅色
    士林區: '#FF7F00', // 橙色
    中山區: '#FFFF00', // 黃色
    內湖區: '#00FF00', // 綠色
    大同區: '#0000FF', // 藍色
    松山區: '#4B0082', // 靛色
    萬華區: '#8B00FF', // 紫色
    中正區: '#FF4500', // 紅橙
    大安區: '#FFD700', // 金黃
    信義區: '#32CD32', // 淺綠
    南港區: '#4682B4', // 鋼藍
    文山區: '#9400D3'  // 深紫
  }
  const conditionArr = []
  Object.keys(districtColor).forEach(district => {
    const color = districtColor[district]
    const arr = [
      `\${district} === '${district}'`,
      `color('${color}')`
    ]
    conditionArr.push(arr)
  })
  conditionArr.push(["true", "color('white')"])
  buildingTileset.style = new Cesium3DTileStyle({
    defines: {
      district: "${feature['addr:district']}",
    },
    color: {
      conditions: conditionArr,
    },
  });
}

</script>
<style scoped>
#cesiumContainer {
  background: gray;
  height: 100vh;
  width: 100%;
}
</style>
