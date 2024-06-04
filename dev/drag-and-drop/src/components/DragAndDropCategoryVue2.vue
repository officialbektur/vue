<template>
	<div class="mb-12 w-96">
		<h4 class="text-white py-3 text-xl">DragAndDropCategoryVue2</h4>
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

<script lang="ts">
	interface Item {
		id: number;
		title: string;
		categoryId: number;
	}

	interface Category {
		id: number;
		title: string;
	}

	export default {
		name: 'DragAndDropCategoryVue2',
		data() {
			return {
				items: [
					{ id: 0, title: 'Audi', categoryId: 0 },
					{ id: 1, title: 'BMW', categoryId: 0 },
					{ id: 2, title: 'Cat', categoryId: 1 },
				] as Item[],
				categories: [
					{ id: 0, title: 'Cars' },
					{ id: 1, title: 'Animals' },
				] as Category[],
				isHovered: {} as Record<number, boolean>,
				dragCounter: {} as Record<number, number>,
				currentItem: null as null | Item,
				onDropCategory: null as null | number,
			};
		},
		methods: {
			onDragStart(e: DragEvent, item: Item) {
				this.currentItem = item;
				this.onDropCategory = item.categoryId;
				e.dataTransfer.effectAllowed = 'move';
			},
			onDragEnter(e: DragEvent, categoryId: number) {
				if (!this.dragCounter[categoryId]) {
					this.dragCounter[categoryId] = 0;
				}
				this.dragCounter[categoryId]++;
				this.isHovered[categoryId] = true;
			},
			onDragLeave(e: DragEvent, categoryId: number) {
				if (this.dragCounter[categoryId]) {
					this.dragCounter[categoryId]--;
					if (this.dragCounter[categoryId] === 0) {
						this.isHovered[categoryId] = false;
					}
				}
			},
			onDrop(e: DragEvent, categoryId: number) {
				if (this.currentItem) {
					this.items = this.items.map((x) => {
						if (x.id === this.currentItem!.id) x.categoryId = categoryId;
						return x;
					});
				}
				this.isHovered[categoryId] = false;
				this.dragCounter[categoryId] = 0;
				this.currentItem = null;
				this.onDropCategory = null;
			},
			onDragEnd() {
				this.onDropCategory = null;
			},
		},
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
		cursor: grab;
	}

	.draggable h5 {
		margin: 0;
	}
</style>
