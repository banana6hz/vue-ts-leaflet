<template>
  <div>
    <bread-crumb v-if="hasBreadCrumb" :updateMap="clickMenuUpdateMap"/>
    <div id="cpn" class="leaflet" :style='{ height: height }' />
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import BreadCrumb from './components/bread-crumb.vue'
import 'leaflet/dist/leaflet.css'
import L from 'leaflet/dist/leaflet-src.js'
import markIcon from 'leaflet/dist/images/marker-icon.png'
import markShadow from 'leaflet/dist/images/marker-shadow.png'

@Component({
  name: 'Leaflet',
  components: { BreadCrumb }
})
export default class extends Vue {
  @Prop({ default: '458px' }) public height!: string
  @Prop({ default: true }) public hasBreadCrumb?: boolean
  public map: any
  public greenIcon: any
  // 点击地图菜单更新地图
  async clickMenuUpdateMap (clickItem) {
    console.log('clickMenuUpdateMap', clickItem.location)
    this.map.setView(clickItem.location, 10)
    L.marker([22.7, 114.7], {icon: this.greenIcon}).addTo(this.map)
  }
  async mounted () {
    //在leaflet中的经纬度坐标与实际坐标位置是相反的，即真实的地理经纬度坐标为[114.398902, 30.518762]
    this.map = L.map('cpn').setView([22.34373, 114.19876], 10);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
    }).addTo(this.map);
    this.greenIcon = L.icon({
      iconUrl: markIcon,
      shadowUrl: markShadow,
      iconSize:     [32, 52],    //  图标的大小    【值1，值2】 为具体你自定义图标的尺寸，比如我图标尺寸是32×52，表示该图标：宽度32像素，高度：52像素，那么值1:就是32，值2：就是52
      shadowSize:   [41, 41], //  影子的大小    【值1，值2】 为具体你自定义阴影图标的尺寸，比如我图标尺寸是41×41，表示该图标：宽度41像素，高度：41像素，那么值1:就是41，值2：就是41
      iconAnchor:   [16, 52],  //  图标将对应标记点的位置 这个是重点， 【值1，值2】，值1：为图标坐标第一个值(即32)的一半，值2：为图标坐标第二个值(即52)
      // shadowAnchor: [4, 62],  // 相同的影子
      popupAnchor:  [1, -38] // 该点是相对于iconAnchor弹出信息的位置  这个是我手动调出来的，文档默认原始值是[-1，-76]，我是去一半值，取一半值调出来的
    });
    // 初始化默认位置
    L.marker([22.3, 114.1], {icon: this.greenIcon}).addTo(this.map)
      .bindPopup('Here')
      .openPopup();
    // 获取当前位置
    // this.map.locate({
    //   setView: true,
    //   maxZoom: 10
    // });
    // this.map.on('locationfound', function (e) {
    //   const radius = e.accuracy / 2
    //   L.marker(e.latlng, {icon: greenIcon}).addTo(map).bindPopup('You are here!').openPopup()
    //   L.circle(e.latlng, radius).addTo(map)
    // });
  }
}
</script>

