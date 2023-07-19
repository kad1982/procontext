<template>
	<div class="wrap">
		<section class="settings">
			<ul>
				<li v-for="item in list" :key="item.id" class="list" :class="item.class">
					<img width="10" height="10" src="./assets/img/arrow.png" alt="стрелочка" @click="toggleCollapse(item)">
					<label :for="item.id" class="list__checkbox">
						<input type="checkbox" :name="item.id" :id="item.id" @click="addCheckSubItem(item)" class="item_list"
							:class="checkLength(item) > 0 ? 'sub_active' : ''" :checked="item.checked">
						<span></span>{{ item.name }}
					</label>
					<ul>
						<li v-for="subItem in item.subItems" :key="subItem.id" class="item">
							<div>
								<input type="checkbox" :checked="subItem.check" @click="addCheckRootItem(item, subItem)"
									class="item_subList">
								{{ subItem.name }}
							</div>
							<div>

								<input type="number" v-model="subItem.quantity" @input="updateItem(subItem)" min="0">
								<input type="color" v-model="subItem.color">
							</div>
						</li>
					</ul>
				</li>
			</ul>
		</section>
		<section class="work">
			<ul class="work_list">
				<li v-for="item in list" :key="item.id">
					<work-table :item="item" @updateSubItem="colorClick" />
				</li>
			</ul>
		</section>
	</div>
</template>
<style scoped>
.wrap {
	display: flex;
	justify-content: space-between;
	align-items: flex-start;
}

.settings {
	width: 100%;
	height: 100vh;
	border: 1px solid black;
	padding: 40px;
}

.settings,
.work {
	flex-basis: 50%;
}

ul {
	list-style: none;
}

input {
	width: 26px;
}

.list.collapsed>ul {
	display: none;
}

img {
	transition: all .3s ease-in-out;
}

.list.collapsed>img {
	transform: rotate(270deg);
}

.item {
	display: flex;
	justify-content: space-between;
	align-items: center;
}

.item div {
	display: flex;
	justify-content: center;
	align-items: center;
}

input[type=number] {
	border: none;
}

.item_list {
	display: none;
	/* <--скрываем дефолтный чекбокс */
}

.list__checkbox span {
	/* <-- стилизируем новый */
	display: inline-block;
	content: url('./assets/img/checkbox_disable.svg');
	width: 14px;
	height: 14px;
}

.item_list:checked+span {
	/* <-- ставим иконку, когда чекбокс включен  */
	display: inline-block;
	content: url('./assets/img/checkbox_active.svg');
	width: 14px;
	height: 14px;
}

.sub_active+span {
	/* <-- ставим иконку, когда чекбокс включен  */
	display: inline-block;
	content: url('./assets/img/checkbox_subactive.svg');
	width: 14px;
	height: 14px;
}

.work_list {
	display: flex;
	flex-direction: column;
	gap: 10px;
}
</style>

<script setup>
import workTable from './components/workTable.vue'
import { reactive } from 'vue';


const list = reactive([
	{
		id: 1,
		name: 'List 1',
		class: '',
		checked: false,
		subItems: [
			{ id: 11, name: 'item 1', quantity: 10, color: '#FF0000', check: true },
			{ id: 12, name: 'item 2', quantity: 16, color: '#FFFF00', check: true },
			{ id: 13, name: 'item 3', quantity: 40, color: '#008000', check: true },
			{ id: 14, name: 'item 4', quantity: 0, color: '#0000FF', check: false }
		]
	},
	{
		id: 2,
		name: 'List 2',
		class: 'collapsed',
		checked: true,
		subItems: [
			{ id: 21, name: 'item 1', quantity: 1, color: '#FF0000', check: true },
			{ id: 22, name: 'item 2', quantity: 2, color: '#FFFF00', check: true },
			{ id: 23, name: 'item 3', quantity: 4, color: '#008000', check: true },
			{ id: 24, name: 'item 4', quantity: 3, color: '#0000FF', check: true }
		]
	},
	{
		id: 3,
		name: 'List 3',
		class: 'collapsed',
		checked: false,
		subItems: [
			{ id: 31, name: 'item 1', quantity: 5, color: '#FF0000', check: true },
			{ id: 32, name: 'item 2', quantity: 6, color: '#FFFF00', check: true },
			{ id: 33, name: 'item 3', quantity: 0, color: '#008000', check: false },
			{ id: 34, name: 'item 4', quantity: 4, color: '#0000FF', check: true }
		]
	},
	{
		id: 4,
		name: 'List 4',
		class: 'collapsed',
		checked: false,
		subItems: [
			{ id: 41, name: 'item 1', quantity: 1, color: '#FF0000', check: true },
			{ id: 42, name: 'item 2', quantity: 6, color: '#FFFF00', check: true },
			{ id: 43, name: 'item 3', quantity: 3, color: '#008000', check: true },
			{ id: 44, name: 'item 4', quantity: 0, color: '#0000FF', check: false }
		]
	},
	{
		id: 5,
		name: 'List 5',
		class: 'collapsed',
		checked: false,
		subItems: [
			{ id: 51, name: 'item 1', quantity: 17, color: '#FF0000', check: true },
			{ id: 52, name: 'item 2', quantity: 16, color: '#FFFF00', check: true },
			{ id: 53, name: 'item 3', quantity: 0, color: '#008000', check: false },
			{ id: 54, name: 'item 4', quantity: 20, color: '#0000FF', check: true }
		]
	}
]);

const toggleCollapse = (item) => {
	item.class === 'collapsed' ? item.class = '' : item.class = 'collapsed';
};

const addCheckSubItem = (item) => {
	item.checked = !item.checked;
	item.subItems.forEach(li => {
		li.check = item.checked;
		if (li.check && li.quantity === 0) li.quantity = 1
		if (!li.check) li.quantity = 0
	});
}

const checkLength = (item) => {
	return item.subItems.filter(li => li.check === true).length
};

const addCheckRootItem = (item, subItem) => {
	subItem.check = !subItem.check;
	if (subItem.quantity === 0 && subItem.check) { subItem.quantity = 1 }
	if (!subItem.check) { subItem.quantity = 0 }
	const allChecked = item.subItems.every(li => li.check === true);
	if (allChecked) {
		item.checked = true
	} else {
		item.checked = false
	}
}

const updateItem = (subItem) => {
	(subItem.quantity <= 0) ? subItem.quantity = 0 : subItem.check = true
};

const colorClick = (event, item) => {
	const data = item.subItems.find(el => el.id === +event);
	data.quantity -= 1;

}
</script>