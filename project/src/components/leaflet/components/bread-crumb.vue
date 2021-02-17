<template>
	<div class="breadcrumb">
		<el-breadcrumb separator="/">
			<el-breadcrumb-item v-for="item in breadCrumbMenuList" :key="item.index">
				<el-menu
					:default-active="item.index"
					class="el-menu-demo"
					mode="horizontal"
					@select="handleSelect"
					background-color="#fff"
					text-color="#545c64"
					active-text-color=’#303133’
				>
					<el-submenu index="1" v-if="item.menuList">
						<template slot="title" style="background-color='#fff'">
							{{item.name}}
						</template>
						<div :index="siteItem.id" v-for="siteItem in item.menuList" :key="siteItem.id" @click="handleMenuClick(siteItem)">
							<el-submenu :index="siteItem.id" v-if="siteItem.menuList">
								<template slot="title">{{siteItem.name}}</template>
								<div :index="buildItem.id" v-for="buildItem in siteItem.menuList" :key="buildItem.id" @click="handleMenuClick(buildItem, siteItem)">
									<el-submenu :index="buildItem.id" v-if="buildItem.menuList">
										<template slot="title">
											{{buildItem.name}}
										</template>
										<el-menu-item :index="floorItem.id" :key="floorItem.id" v-for="floorItem in buildItem.menuList">{{floorItem.name}}</el-menu-item>
									</el-submenu>
									<el-menu-item :index="buildItem.buildId" v-else>{{buildItem.name}}</el-menu-item>
								</div>
							</el-submenu>
							<el-menu-item :index="siteItem.id" v-else>{{siteItem.name}}</el-menu-item>
						</div>
					</el-submenu>
					<el-menu-item :index="item.id" v-else>{{item.name}}</el-menu-item>
				</el-menu>
			</el-breadcrumb-item>
		</el-breadcrumb>
	</div>
</template>
<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
interface BreadCrumbMenuList {
	name: string;
	id: string;
	level: number;
	location: number[];
	menuList?: SiteMenu[];
}

interface SiteMenu {
	name: string;
	id: string;
	level: number;
	location: number[];
	menuList?: BuildMenu[];
}

interface BuildMenu {
	name: string;
	id: string;
	level: number;
	location: number[];
	menuList?: FloorMenu[];
}

interface FloorMenu {
	name: string;
	id: string;
	level: number;
	location: number[];
}
@Component({
  name: 'BreadCrumb',
})
export default class BreadCrumb extends Vue {
  @Prop({ type: Function, required: false, default: {} }) public updateMap?: any
	private breadCrumbMenuList: BreadCrumbMenuList[] = []

	created () {
		this.breadCrumbMenuList = [
			{
				name: 'World',
				id: '0',
				level: 0,
				location: [22.34373, 114.1987],
				menuList: [
					{
						name: '河源',
						id: '81',
						level: 1,
						location: [23.74626, 114.69780],
					},
					{
						name: '深圳',
						id: '82',
						level: 1,
						location: [],
						menuList: [
							{
								name: '龙岗区',
								id: '21',
								level: 2,
								location: []
							},
							{
								name: '南山区',
								location: [],
								id: '22',
								level: 2,
								menuList: [
									{
										name: '陈同学家🏠',
										id: '221',
										level: 3,
										location: [],
									}
								]
							}
						]
					},
					{
						name: '广州',
						id: '83',
						level: 1,
						location: [],
						menuList: [
							{
								name: '天河区',
								id: '330',
								level: 2,
								location: [],
								menuList: [
									{
										name: '搬砖工地🧱',
										level: 3,
										id: '311',
										location: [],
									}
								]
							},
							{
								name: '增城区',
								id: '367',
								level: 2,
								location: [],
								menuList: [
									{
										name: '山里⛰️',
										id: '321',
										level: 3,
										location: [],
									},
									{
										name: '水里🌊',
										id: '322',
										level: 3,
										location: [],
									}
								]
							}
						]
					}
				]
			}
		]
	}
	// 从World层选择floor
	handleSelect (key, keyPath) {
		if (keyPath.length == 4) {
			this.breadCrumbMenuList.length = 1
			const siteId = keyPath[1]
			const buildId = keyPath[2]
			const floorId = keyPath[3]
			const buildList = this.breadCrumbMenuList[0].menuList!.filter((item) => item.id === siteId)[0]
			const floorList = buildList.menuList!.filter((item) => item.id === buildId)[0]
			const floorItem = floorList.menuList!.filter((item) => item.id === floorId)[0]
			this.breadCrumbMenuList.push(buildList)
			this.breadCrumbMenuList.push(floorList)
			this.breadCrumbMenuList.push(floorItem)
			this.handleMapChange(floorItem) // 更新地图
		}
	}

	handleMenuClick (clickItem, preItem) {
		if (this.breadCrumbMenuList.length === clickItem.level) {
			this.breadCrumbMenuList.push(clickItem)
		} else if (this.breadCrumbMenuList.length > clickItem.level) {
			this.breadCrumbMenuList.length = clickItem.level
			this.breadCrumbMenuList.push(clickItem)
		}
		// 跳两层选择
		if (preItem) {
			this.breadCrumbMenuList.length = clickItem.level - 1
			this.breadCrumbMenuList.push(preItem)
			this.breadCrumbMenuList.push(clickItem)
		}
		this.handleMapChange(clickItem) // 更新地图
	}

	handleMapChange (clickItem) {
		this.updateMap(clickItem) // 传给父组件
	}
}
</script>
<style lang="scss">
.breadcrumb {
 padding-bottom: 10px;
}
.el-menu.el-menu--horizontal {
 border-bottom: none;
}
.el-menu--horizontal>.el-submenu .el-submenu__icon-arrow {
 display: none;
}
.el-breadcrumb__item ul {
 display: inline-block;
}
.el-breadcrumb__item {
 display: flex;
}
.el-breadcrumb__separator {
 line-height: 60px;
}
.el-menu--horizontal>.el-submenu.is-active .el-submenu__title, .el-menu--horizontal>.el-menu-item.is-active {
  border-bottom:  none;
}
.el-menu--popup {
	background: #fff !important;
}
</style>
