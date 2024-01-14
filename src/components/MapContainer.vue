<script setup>
import { onMounted, onUnmounted } from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";
import sitesData from "@/data/sites.json";

let map = null;

onMounted(() => {
  console.log(sitesData);
  AMapLoader.load({
    key: "a0379d7a665567d32886411f804826ff", // 申请好的Web端开发者Key，首次调用 load 时必填
    version: "2.0", // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
    plugins: ["AMap.MarkerClusterer", "AMap.Satellite"], // 需要使用的的插件列表，如比例尺'AMap.Scale'等
  })
    .then((AMap) => {
      map = new AMap.Map("container", {
        // 设置地图容器id
        viewMode: "3D", // 是否为3D地图模式
        zoom: 8, // 初始化地图级别
        center: [120.270265,30.171213], // 初始化地图中心点位置
        layers: [
          // 卫星
          new AMap.TileLayer.Satellite(),
          // 路网
          new AMap.TileLayer.RoadNet(),
        ],
      });

      // // add marker cluster
      // var points = [
      //   {lnglat: ["108.939621", "34.343147"] },
      //   {lnglat: ["113.370643", "22.938827"] },
      //   {lnglat: ["112.985037", "23.15046"] },
      //   {lnglat: ["110.361899", "20.026695"] },
      //   {lnglat: ["121.434529", "31.215641"] },
      //   {lnglat: ["120.23473", "30.24577"]}
      // ];
      // map.plugin(["AMap.MarkerClusterer"],function() {
      //   cluster = new AMap.MarkerClusterer(
      //   map,     // 地图实例
      //   points, // 海量点数据，数据中需包含经纬度信息字段 lnglat
      //   );
      // });
      // add labelslayer

      var textStyle = {
        fontSize: 12,
        fontWeight: "normal",
        fillColor: "#fff",
        // fillColor: 'rgb(255, 215, 0)',
        // strokeColor: '#fff',
        // strokeWidth: 2,
        // fold: true,
        padding: "2, 5",
        backgroundColor: "rgb(00,00,256)",
        borderColor: "#fff",
        borderWidth: 1,
      };
      var markers = [];

      // 初始化 labelMarker
      for (var i = 0; i < sitesData.sites.length; i++) {
        var site = sitesData.sites[i];

        var labelMarker = new AMap.LabelMarker({
          name: site.name,
          position: site.position,
          text: {
            content: site.text.content,
            direction: "center",
            style: textStyle,
          },
          extData: site.vedio_url
        }).on("click", (e) => {
          var url = e.target.getExtData();
          // console.log(e.target.extData);
          window.open(url, "_blank");
          // console.log(e);
        });
        // console.log(labelMarker)

        markers.push(labelMarker);
      }

      var layer = new AMap.LabelsLayer({
        zooms: [3, 20],
        zIndex: 1000,
        collision: false,
        allowCollision: false,
      });
      layer.add(markers);

      // 图层添加到地图
      map.add(layer);
    })
    .catch((e) => {
      console.log(e);
    });
});

onUnmounted(() => {
  map?.destroy();
});
</script>

<template>
  <div id="container"></div>
</template>

<style scoped>
#container {
  width: 100%;
  height: 800px;
}
</style>
