<template>
	<div class="work_wrap">
		<div>
			{{ item.name }}
			<button class="typeButton" @click="actionButton" v-if="showButton"><span v-if="!data.showMix">Перемешать</span><span
					v-else>Сортировать</span></button>
		</div>
		<div v-if="!data.showMix">
			<ul v-for="subItem in item.subItems" :key="subItem.id" class="work_item">
				<li v-for="index in subItem.quantity" :key="index">
					<span class="work_color" :style="{ 'background-color': subItem.color }" @click="colorClick($event, item)"
						:id="subItem.id"></span>
				</li>
			</ul>
		</div>
		<div v-else>
			<ul>
				<li v-for="index in mixArray" :key="index">
					<span class="work_color" :style="{ 'background-color': index.color }" @click="colorClick($event, mixArray)"
						:id="index.id"></span>
				</li>
			</ul>
		</div>
	</div>
</template>
<style scoped>
.work_color {
	display: inline-block;
	width: 10px;
	height: 10px;
}

ul {
	display: flex;
	flex-wrap: wrap;
	gap: 10px;
	padding: 0;
	list-style: none;

}

.work_wrap {
	padding: 10px;
	border: 1px solid black;
}

.work_wrap>div:nth-child(1) {
	display: flex;
	justify-content: space-between;
	align-items: center;
}

.work_wrap>div:nth-child(2) {
	display: block;
}

.next {
	display: none;
}

.typeButton {
	background-color: blue;
	color: white;
	border: none;
	border-radius: 20px;
	padding: 2px 5px;
}
</style>
<script setup>
import { toRefs, defineProps, computed, defineEmits } from 'vue'
import { reactive } from 'vue';
const data = reactive(
	{ showMix: false }
)
const props = defineProps({
	item: {
		type: Object
	},
})
const { item } = toRefs(props)
const actionButton = () => {
	data.showMix = !data.showMix
}

function shuffle(array) {
	let currentIndex = array.length, randomIndex;
	while (currentIndex != 0) {
		randomIndex = Math.floor(Math.random() * currentIndex);
		currentIndex--;
		[array[currentIndex], array[randomIndex]] = [
			array[randomIndex], array[currentIndex]];
	}
	return array;
}

const mixArray = computed(() => {
	const resultArray = []
	const table = item.value.subItems ? item.value.subItems : []
	table.forEach(element => {
		for (let i = 0; i < element.quantity; i++) {
			resultArray.push(element)
		}
	}
	)
	return resultArray ? shuffle(resultArray) : []
})

const showButton = computed(() => {
	const sumItemQuantily = item.value.subItems.reduce(
		(sum, currentValue) => sum + currentValue.quantity,
		0
	);
	return (sumItemQuantily > 0) ? true : false
})

const emits = defineEmits(['updateSubItem'])

const colorClick = (event, item) => {
	emits('updateSubItem', event.target.id, item)
}

</script>