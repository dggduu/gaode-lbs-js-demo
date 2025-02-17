<template>
<div id="container">
  <input id="input_test" placeholder="搜索地点">
</div>
<div class="data-show">
  <button @click="createPoly()" id="createPoly">从标记点创建图形</button>
  <button @click="delPoly()" id="createPoly">删除选区</button>
  <p class="header2">marker信息：</p>
  <table class="data-arr">
    <th>index</th>
    <th>纬度：</th>
    <th>经度：</th>
    <th>创建时间：</th>
    <th>操作：</th>
    <tr v-for="(marker,index) in MarkerArr" :key="index">
      <td>{{index+1}}</td>
      <td>{{ marker.lat}}</td>
      <td>{{marker.lng}}</td>
      <td>{{marker.Time}}</td>
      <td><div class="opeartor">
        <button @click="gotoMarker(marker)" id="nav-btn">定位</button>
        <button @click="DeleteMarker(index)" id="del-btn">删除</button>
      </div>
      </td>
    </tr>
  </table>
</div>
</template>


<script setup>
import { onMounted, onUnmounted,ref} from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";

let map = null;
let MarkerArr =ref([]);
let Poly =null;
onMounted(() => {
  window._AMapSecurityConfig = {
    securityJsCode: "    ",
  };
  AMapLoader.load({
    key: "    ",
    version: "2.0",
    plugins: ["AMap.Scale",
              "AMap.ToolBar",
              'AMap.PlaceSearch',
              'AMap.AutoComplete'
              ],
  })
    .then((AMap) => {
      map = new AMap.Map("container", {
        viewMode: "2D",
        zoom: 15,
        center: [112.946191,28.183942],
      });


      const toolbar = new AMap.ToolBar();//toolbar
        map.addControl(toolbar);


      var autoOptions = {input: "input_test"};//自动补全
      var auto = new AMap.AutoComplete(autoOptions);
      var placeSearch = new AMap.PlaceSearch({
        map: map
      });
      auto.on("select",(e)=>{
          placeSearch.setCity(e.poi.adcode);
          placeSearch.search(e.poi.name,function (status,result){
          console.log("Status:",status,"result",result);
          });
      });

      map.on('click', (e) => {
        const marker = new AMap.Marker({
          position: e.lnglat,
          map: map,
        });
        const now =new Date().toDateString();
        MarkerArr.value.push({ lng: e.lnglat.lng, lat: e.lnglat.lat,Time:now,marker});
        console.log(MarkerArr.value);
        const markerIndex = MarkerArr.value.length + 1;

        const infoWindowContent = `
          <div>
            <p><strong>Index:</strong></br> ${markerIndex}</p>
            <p><strong>纬度:</strong></br> ${e.lnglat.lat}</p>
            <p><strong>经度:</strong></br> ${e.lnglat.lng}</p>
            <p><strong>创建时间:</strong></br> ${now}</p>
          </div>
        `;

        const infoWindow = new AMap.InfoWindow({
          content: infoWindowContent,
          offset: new AMap.Pixel(0, -30),
        });//自定义信息窗口
        marker.on('click', () => {
          infoWindow.open(map, marker.getPosition());
        });
      });
    })
    .catch((e) => {
      console.log(e);
    });
});


const DeleteMarker = (index)=>{
        if(MarkerArr.value[index].marker){
          MarkerArr.value[index].marker.setMap(null);
        }
        MarkerArr.value.splice(index,1);
      }
const gotoMarker=(marker)=>{
  map.setZoomAndCenter(17,[marker.lng,marker.lat],false,10);
}

const createPoly=()=>{
  if(Poly){
    Poly.setMap(null);
  }
  if(MarkerArr.value.length>=3){
      const pathArr=MarkerArr.value.map(marker=>[marker.lng,marker.lat]);

      Poly=new AMap.Polygon({
          path: pathArr,
          fillColor: "#f8e9d0",
          strokeOpacity: 1,
          fillOpacity: 0.5,
          strokeColor: "#2b8cbe",
          strokeWeight: 1,
          strokeStyle: "dashed",
          strokeDasharray: [5, 5],
        });
        Poly.setMap(map);
  }
}

const delPoly =()=>{
  if(Poly){
    Poly.setMap(null);
  }
}

onUnmounted(() => {
  map?.destroy();
});
</script>

<style >
#container {
  display: flex;
  margin: auto;
  height: 600px;
}
#input_test{
  margin-top: 5px;
  overflow: visible;
  height: 15px;
  z-index: 1;
  border-radius: 0px 10px 10px 0px;
  padding: 5px;
  border: none;
  box-shadow: 0px 4px 4px rgba(0, 0, 0,0.2);
}
.amap-sug-result{
  border-radius: 0px 10px 10px 0px;
  padding: 5px;
  margin-top: 3px;
  overflow:hidden;
}
.amap-content-body{
  border-radius: 10px;
  padding: 5px 5px 0px 5px;
}
.amap-info-content{
  border-radius: 10px;
  padding: 10px;
  font-size: 0.8rem;
}
.amap-info-content p{
  margin: 5px ;
}

.data-show{
  margin: 16px 8px;
  padding: 16px;
  height: 100px;
  font-size: 0.8rem;
  border-radius: 10px;
  background-color: bisque;
  text-align: center;
  overflow-y: scroll;
}
.header2{
  font-weight: 600;
  margin-top: 0px;
}

.data-arr{
  width: 100%;
}
button{
  border-radius: 10px;
  border: 1px grey solid;
  font-size: 0.75rem;
  background-color: white;
  padding: 5px;
  font-weight: 500;
}
#del-btn{
  color: red;
  margin-left: 3px;
}
#createPoly {
  display: inline-block;
  padding: 5px;
  margin-top: 0px;
  margin-bottom: 10px;
  margin-left: 10px;
}
</style>
