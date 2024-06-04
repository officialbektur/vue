<template>
	<div class="mb-12 w-96">
		<h4 class="text-white py-3 text-xl">DragAndDropCategoryVue3</h4>
		<div
			v-for="category in categories"
			:key="category.id"
			@drop.prevent="onDrop($event, category.id)"
			@dragover.prevent
			@dragenter.prevent="onDragEnter($event, category.id)"
			@dragleave.prevent="onDragLeave($event, category.id)"
			@dragend.prevent="onDragEnd"
			class="w-full droppable"
			:class="{
				_hover: isHovered[category.id],
				_onDrop: onDropCategory === category.id,
			}"
		>
			<h4>{{ category.title }}</h4>
			<div
				v-for="item in items.filter((x) => x.categoryId === category.id)"
				:key="item.id"
				@dragstart="onDragStart($event, item)"
				draggable="true"
				class="w-full draggable"
			>
				<h5 class="text-black">{{ item.title }}</h5>
			</div>
		</div>
	</div>
</template>

<script setup lang="ts">
	import { ref } from 'vue';

	interface Item {
		id: number;
		title: string;
		categoryId: number;
	}

	interface Category {
		id: number;
		title: string;
	}

	const items = ref<Item[]>([
		{ id: 0, title: 'Audi', categoryId: 0 },
		{ id: 1, title: 'BMW', categoryId: 0 },
		{ id: 2, title: 'Cat', categoryId: 1 },
	]);
	const categories = ref<Category[]>([
		{ id: 0, title: 'Cars' },
		{ id: 1, title: 'Animals' },
	]);
	const isHovered = ref<Record<number, boolean>>({});
	const dragCounter = ref<Record<number, number>>({});
	const currentItem = ref<Item | null>(null);
	const onDropCategory = ref<number | null>(null);

	const onDragStart = (e: DragEvent, item: Item) => {
		currentItem.value = item;
		onDropCategory.value = item.categoryId;
		e.dataTransfer.effectAllowed = 'move';
	};

	const onDragEnter = (e: DragEvent, categoryId: number) => {
		if (!dragCounter.value[categoryId]) {
			dragCounter.value[categoryId] = 0;
		}
		dragCounter.value[categoryId]++;
		isHovered.value = { ...isHovered.value, [categoryId]: true };
	};

	const onDragLeave = (e: DragEvent, categoryId: number) => {
		if (dragCounter.value[categoryId]) {
			dragCounter.value[categoryId]--;
			if (dragCounter.value[categoryId] === 0) {
				isHovered.value = { ...isHovered.value, [categoryId]: false };
			}
		}
	};

	const onDrop = (e: DragEvent, categoryId: number) => {
		if (currentItem.value) {
			const previousCategoryId = currentItem.value.categoryId;
			items.value = items.value.map((x) => {
				if (x.id === currentItem.value.id) x.categoryId = categoryId;
				return x;
			});
		}
		isHovered.value = { ...isHovered.value, [categoryId]: false };
		dragCounter.value[categoryId] = 0;
		currentItem.value = null;

		onDropCategory.value = null;
	};

	const onDragEnd = () => {
		onDropCategory.value = null;
	};
</script>

<style scoped>
	.droppable {
		padding: 15px;
		border-radius: 5px;
		background: #2c3e50;
		margin-bottom: 10px;
		transition: background 0.3s;
	}

	.droppable._hover {
		background: yellowgreen;
	}

	.droppable._onDrop {
		border: 2px dashed red;
	}

	.droppable h4 {
		color: white;
	}

	.draggable {
		background: white;
		padding: 5px;
		border-radius: 5px;
		margin-bottom: 5px;
	}

	.draggable h5 {
		margin: 0;
	}
</style>
