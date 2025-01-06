<template>
  <div id="cesiumContainer"></div>
</template>
<script setup>
import { Cartesian3, Math as CesiumMath, Terrain, Viewer, createOsmBuildingsAsync, } from "cesium";
import { onMounted } from "vue";
onMounted(() => {
  const viewer = new Viewer("cesiumContainer", {
    terrain: Terrain.fromWorldTerrain(),
  });
  viewer.camera.flyTo({
    destination: Cartesian3.fromDegrees(121.5658, 25.02323, 550),
    orientation: {
      heading: CesiumMath.toRadians(0.0), //圍繞 Y軸（垂直方向） 旋轉
      pitch: CesiumMath.toRadians(-15.0), //圍繞 X軸（水平方向） 旋轉
    },
  });
  createOsmBuildingsAsync().then((buildingTileset) => {
    viewer.scene.primitives.add(buildingTileset);
  });
})

</script>
<style scoped>
#cesiumContainer {
  background: gray;
  height: 100vh;
  width: 100%;
}
</style>
