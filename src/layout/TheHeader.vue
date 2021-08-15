<template>
	<div class="header-tool">
		<div v-show="data.routeName === 'AddNeed'" class="add-need">
			<i class="el-icon-back add-i" @click="back"></i>
			<span style="line-height: 60px; font-size: 19px; font-weight: 800">{{ title }}</span>
		</div>
		<el-menu
			v-show="data.routeName !== 'AddNeed'"
			:default-active="data.activeIndex"
			class="el-menu-demo"
			mode="horizontal"
			@select="handleSelect"
			active-text-color="#0087FF"
		>
			<el-menu-item
				:style="data.activeIndex === item.index ? 'background-color:#F2F6FA' : ''"
				v-for="(item, index) in data.menuList"
				:key="index"
				:index="item.index"
				@click="changeType(item)"
			>
				{{ item.title }}
			</el-menu-item>
		</el-menu>
		<div class="icon-item">
			<span class="tool-item">
				<img :src="data.user.pictureBase64" style="border: solid 1px #d1d1f1" />
				欢迎您，{{ data.user.userName }}
			</span>
			<div class="exit">
				<SwitchButton style="height: 28px; position: absolute; top: -8px" />
			</div>
		</div>
	</div>
	<div class="line"></div>
</template>
<script setup>
import { SwitchButton } from '@element-plus/icons'
import { onMounted, computed, reactive, toRaw, ref } from 'vue'
import { useRoute, onBeforeRouteUpdate, useRouter } from 'vue-router'
import { settingMenu } from '@/common/models'
import { useStore } from 'vuex'
import bus from 'vue3-eventbus'

const router = useRouter()
const route = useRoute()
const store = useStore()
const title = ref('新建需求')
bus.on('chengeTitle', val => {
	title.value = val
})
const back = () => {
	router.go(-1)
}
const data = reactive({
	menuList: [],
	activeIndex: '0', // 必须是字符串格式
	routeName: '',
	user: computed(() => store.state.userInfo)
})

onMounted(async () => {
	data.routeName = toRaw(route.name)
	confirmMenu(data.routeName)
})

onBeforeRouteUpdate(to => {
	confirmMenu(to.name)
	data.routeName = to.name
})
const confirmMenu = routeName => {
	switch (routeName) {
		case 'Setting':
			data.menuList = settingMenu
			break
		case 'AddNeed':
			data.menuList = []
			break
		case 'NeedList':
			data.menuList = []
			break
		default:
			break
	}
}
const handleSelect = (key, keyPath) => {
	data.activeIndex = key
}

const changeType = item => {
	bus.emit('foo', item)
}
</script>
<style scoped="scoped">
.header-tool {
	display: flex;
	justify-content: space-between;
	width: 97%;
	height: 100%;
	color: #1f263e;
}
.add-need {
	display: flex;
}
.add-i {
	height: 100%;
	width: 50px;
	line-height: 60px;
	font-size: 23px;
	text-align: start;
}
.el-menu-demo {
	width: 70%;
}
.exit {
	position: relative;
	display: inline-block;
	width: 80px;
	height: 18px;
	font-size: 22px;
	line-height: 18px;
	border-left: 2px solid #cdd0da;
	margin-left: 20px;
	bottom: 5px;
}

.header-tool span {
	margin-left: 10px;
	cursor: pointer;
}
.item {
	margin: 0 10px;
}
.header-logo a {
	text-decoration: none;
	color: #fff;
}
.el-steps {
	width: 50%;
	margin: 20px;
}
.tool-item {
	align-items: center;
	display: inline-flex;
}
.tool-item img {
	width: 32px;
	height: 32px;
	border-radius: 50%;
	background: #fff;
	margin-right: 20px;
}
.icon-item {
	margin-top: 16px;
	float: left;
}
.el-menu-item {
	font-size: 19px;
	font-weight: 800;
}
</style>
