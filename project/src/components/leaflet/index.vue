<template>
  <div>
    <bread-crumb v-if='hasBreadCrumb' />
    <div id="cpn" class="leaflet" :style='{ height: height }' />
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import BreadCrumb from './components/bread-crumb.vue'

@Component({
  name: 'Leaflet',
  components: { BreadCrumb }
})
export default class extends Vue {
  @Prop({ default: '458px' }) public height!: string
  @Prop({ default: false }) public hasBreadCrumb?: boolean
  // 点击地图菜单更新地图
  // async clickMenuUpdateMap (clickItem) {
  //   console.log('clickMenuUpdateMap', clickItem)
  // }
}
</script>

