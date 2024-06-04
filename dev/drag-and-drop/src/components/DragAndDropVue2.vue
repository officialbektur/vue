<template>
	<h4 class="text-white py-3 text-xl">DragAndDropVue2</h4>

	<div
		class="form__images image w-96 flex flex-wrap gap-2"
		:class="{
			_sending: isReadOnly,
			'_has-file': isSelectedFile,
		}"
	>
		<div
			v-for="(imageItem, index) in imageItems"
			:key="index"
			@drop.prevent="handleDrop($event, index)"
			@dragover.prevent
			@dragenter.prevent="handleDragEnter($event, index)"
			@dragleave.prevent="handleDragLeave($event, index)"
			@dragend.prevent="handleDragEnd"
			@click="moveImageToAddress($event, imageItem)"
			class="image__block image-block"
			:class="{
				_hovered: dragCounter[index],
				_dropped: droppedIndex === index,
				_current: currentItemTouch === index,
			}"
		>
			<div
				@dragstart="handleDragStart($event, imageItem)"
				draggable="true"
				class="image-block__content"
				:class="{ _active: imageItem.src }"
			>
				<input
					:readonly="isReadOnly"
					:disabled="isReadOnly"
					accept=".jpg, .png, .jpeg"
					type="file"
					name="images[]"
					@change="handleFileChange($event, index)"
					multiple
					:ref="`input_${index}`"
					class="image-block__input"
				/>
				<div class="image-block__preview">
					<img
						v-if="imageItem.src"
						:src="imageItem.src"
						:alt="imageItem.src"
						class="image-block__img"
					/>
				</div>
				<button
					:disabled="isReadOnly"
					type="button"
					class="image-block__close"
					@click.prevent="resetImage(index)"
				>
					<svg
						viewBox="0 0 448 512"
						xmlns="http://www.w3.org/2000/svg"
					>
						<path
							d="M432 256c0 17.69-14.33 32.01-32 32.01H256v144c0 17.69-14.33 31.99-32 31.99s-32-14.3-32-31.99v-144H48c-17.67 0-32-14.32-32-32.01s14.33-31.99 32-31.99h144v-144C192 62.32 206.33 48 224 48s32 14.32 32 32.01v144h144c17.7-.01 32 14.29 32 31.99z"
						/>
					</svg>
				</button>
				<div class="image-block__placeholder image-block-placeholder">
					<div class="image-block-placeholder__icon">
						<svg
							viewBox="0 0 48 48"
							xmlns="http://www.w3.org/2000/svg"
						>
							<path
								fill-rule="evenodd"
								d="M30 6l3.66 4H40c2.2 0 4 1.8 4 4v24c0 2.2-1.8 4-4 4H8c-2.2 0-4-1.8-4-4V14c0-2.2 1.8-4 4-4h6.34L18 6h12zm-6 13.6a6.4 6.4 0 100 12.8 6.4 6.4 0 000-12.8zM24 36c-5.52 0-10-4.48-10-10s4.48-10 10-10 10 4.48 10 10-4.48 10-10 10z"
							/>
						</svg>
					</div>
					<div class="image-block-placeholder__title">Добавьте фото</div>
				</div>
				<button
					v-if="index !== 0 && !currentItem"
					:disabled="isReadOnly"
					type="button"
					class="image-block__move image-block__move-current"
					@click="startMoveImage(imageItem)"
				>
					Переместить
				</button>
				<button
					v-if="currentItem && currentItem.index !== imageItem.index"
					type="button"
					class="image-block__move image-block__move-target"
				>
					Сюда
				</button>
			</div>
			<div
				v-if="index === 0"
				class="image-block__title"
				:class="{
					_done: currentItem,
				}"
			>
				Главная
			</div>
		</div>
	</div>
</template>

<script lang="ts">
	import { defineComponent } from 'vue';

	interface ImageContract {
		id: number;
		key: number;
		index: number;
		src: string | null;
		file: File | null;
	}

	export default defineComponent({
		name: 'DragAndDropVue3',
		props: {
			imagesCount: {
				type: Number,
				required: true,
			},
			isReadOnly: {
				type: Boolean,
				required: true,
			},
		},
		data() {
			return {
				imageItems: [] as ImageContract[],
				dragCounter: [] as number[],
				currentItem: null as ImageContract | null,
				droppedIndex: null as number | null,
				currentItemTouch: null as number | null,
			};
		},
		created() {
			this.initializeImageItems();
		},
		methods: {
			initializeImageItems() {
				this.imageItems = Array.from(
					{ length: this.imagesCount },
					(_, index) => ({
						id: null,
						key: index + 1,
						index: index,
						src: null,
						file: null,
					})
				);
				this.dragCounter = Array(this.imagesCount).fill(0);
			},
			handleFileChange(event, index: number) {
				const selectedFiles = event.target.files;

				this.resetMove();
				this.resetDrop(index);

				if (selectedFiles && selectedFiles.length > 0) {
					const selectedFilesLength = Math.min(
						selectedFiles.length,
						this.imagesCount
					);
					const files = Array.from(selectedFiles).slice(0, selectedFilesLength);
					if (files.length > 1) {
						this.handleMultipleFiles(files, index);
					} else {
						this.uploadFile(files[0], index);
					}
				} else {
					this.removeImage(index);
				}
			},
			handleMultipleFiles(files: FileList, index: number) {
				for (let i = 0; i < files.length; i++) {
					this.handleFileInput(files[i], index);
				}
			},
			handleFileInput(file: File, index: number) {
				const images = Array.prototype.concat(
					this.imageItems.slice(index),
					this.imageItems.slice(0, index)
				);

				const imageItem = images.find((res) => res.file === null);

				if (imageItem) {
					return this.uploadFile(file, imageItem.index);
				}

				this.uploadFile(file, index);
			},
			uploadFile(file: File, index: number) {
				if (this.validateFile(file, index)) {
					const input = this.$refs[`input_${index}`][0];

					// Set files to input and ImageContract object
					const newFile =
						new ClipboardEvent('').clipboardData || new DataTransfer();
					newFile.items.add(file);
					input.files = newFile.files;
					this.imageItems[index].file = newFile.files;

					// Set src to ImageContract object
					let reader = new FileReader();
					reader.onload = (e) => {
						this.imageItems[index].src = e.target.result;
					};
					reader.readAsDataURL(file);
				}
			},
			validateFile(file: File, index: number, maxSizeMB = 5): boolean {
				const allowedTypes = ['image/jpeg', 'image/png', 'image/webp'];
				if (!allowedTypes.includes(file.type)) {
					alert('Разрешены только изображения форматов: jpg, jpeg и png!');
					this.removeImage(index);
					return false;
				}

				const isTooLarge = file.size > maxSizeMB * 1024 * 1024;
				if (isTooLarge) {
					alert(
						`Файл должен быть не более ${maxSizeMB} МБ, а ваш файл составляет: ` +
							(file.size / 1024 / 1024).toFixed(2) +
							' МБ'
					);
					this.removeImage(index);
					return false;
				}
				return true;
			},
			removeImage(index: number) {
				this.imageItems[index].src = null;
				this.imageItems[index].file = null;
				this.$refs[`input_${index}`][0].value = null;
			},
			resetImage(index: number) {
				this.removeImage(index);
			},
			handleDragStart(e: DragEvent, imageItem: ImageContract) {
				this.currentItem = imageItem;
				this.droppedIndex = imageItem.index;
				e.dataTransfer.effectAllowed = 'move';
			},
			handleDragEnter(e: DragEvent, index: number) {
				if (!this.dragCounter[index]) {
					this.dragCounter[index] = 0;
				}
				this.dragCounter[index]++;
			},
			handleDragLeave(e: DragEvent, categoryId: number) {
				if (this.dragCounter[categoryId]) {
					this.dragCounter[categoryId]--;
				}
			},
			handleDrop(e: DragEvent, index: number) {
				if (this.currentItem) {
					this.moveImage(this.imageItems[index], this.currentItem);
				}
				this.resetDrop(index);
			},
			resetDrop(index: number) {
				this.dragCounter[index] = 0;
				this.currentItem = null;
				this.droppedIndex = null;
			},
			handleDragEnd() {
				this.droppedIndex = null;
			},
			moveImage(targetItem: ImageContract, currentItem: ImageContract) {
				this.moveImageContract(targetItem, currentItem);
				this.updateInputs(currentItem.index, targetItem.index);
			},
			moveImageContract(targetItem: ImageContract, currentItem: ImageContract) {
				[targetItem.index, currentItem.index] = [
					currentItem.index,
					targetItem.index,
				];
				[
					this.imageItems[targetItem.index],
					this.imageItems[currentItem.index],
				] = [
					this.imageItems[currentItem.index],
					this.imageItems[targetItem.index],
				];
			},
			startMoveImage(targetItem: ImageContract) {
				this.currentItem = targetItem;
				this.currentItemTouch = targetItem.index;
			},
			moveImageToAddress(event, currentItem: ImageContract) {
				if (this.currentItem && this.currentItem.index !== currentItem.index) {
					event.preventDefault();
					this.moveImage(this.currentItem, currentItem);
					this.resetMove();
				}
			},
			resetMove() {
				this.currentItem = null;
				this.currentItemTouch = null;
			},
			updateInputs(currentIndex: number, targetIndex: number) {
				const currentInput = this.$refs[`input_${currentIndex}`][0];
				const targetInput = this.$refs[`input_${targetIndex}`][0];

				const currentFiles = currentInput.files;
				const targetFiles = targetInput.files;

				currentInput.files = targetFiles;
				targetInput.files = currentFiles;
			},
		},
		computed: {
			isSelectedFile() {
				return this.imageItems.find((res) => res.src !== null);
			},
		},
	});
</script>
