<script lang="ts">
  import "cesium/Build/Cesium/Widgets/widgets.css";
  import * as Cesium from "cesium";

  const ACCESS_TOKEN = import.meta.env.VITE_CESIUM_ACCESS_TOKEN;

  Cesium.Ion.defaultAccessToken = ACCESS_TOKEN;

  let container: null | Element = $state(null);
  $effect(() => {
    if (container) {
      const viewer = new Cesium.Viewer(container, {
        // baseLayer: Cesium.ImageryLayer.fromWorldImagery({
        // 	style: Cesium.IonWorldImageryStyle.AERIAL_WITH_LABELS
        // }),
        baseLayerPicker: false,
        shadows: false,
        terrain: Cesium.Terrain.fromWorldTerrain(),
      });
      viewer.scene.skyAtmosphere.show = true;

      const latitude = 23.0225;
      const longitude = 72.5714;
      const altitude = 100; // Altitude in meters
      const heading = 0;
      const pitch = -20.0;
      const roll = 0.0;
      const orientation = {
        heading: Cesium.Math.toRadians(heading),
        pitch: Cesium.Math.toRadians(pitch),
        roll: Cesium.Math.toRadians(roll),
      };
      const position = Cesium.Cartesian3.fromDegrees(
        longitude,
        latitude,
        altitude,
      );
      viewer.scene.camera.setView({
        destination: position,
        orientation,
      });
      (async () => {
        const buildingTileset = await Cesium.createOsmBuildingsAsync();
        viewer.scene.primitives.add(buildingTileset);
      })();
      viewer.scene.shadowMap.enabled = true;
      viewer.scene.shadowMap.darkness = 0.3;
      viewer.scene.shadowMap.fadingEnabled = false;
      viewer.scene.shadowMap.size = 4096;
      viewer.scene.shadowMap.maximumDistance = 300;
      viewer.scene.light = new Cesium.SunLight();
      viewer.scene.shadowMap.fadingEnabled = true;
      viewer.clock.currentTime = Cesium.JulianDate.fromDate(
        new Date(Date.UTC(2024, 11, 24, 12, 0, 0)),
      );
      viewer.scene.requestRender();
    }
  });
</script>

<div bind:this={container} class="cesiumContainer"></div>

<style>
  .cesiumContainer {
    width: 100dvw;
    height: 100dvh;
  }
</style>
