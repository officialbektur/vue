<template>
	<!-- <DragAndDropCategoryVue2></DragAndDropCategoryVue2> -->
	<!-- <DragAndDropCategoryVue3></DragAndDropCategoryVue3> -->
	<DragAndDrop v-model="images" :is-read-only="isReadOnly"></DragAndDrop>
	<DragAndDropVue2 :imagesCount="5" :is-read-only="isReadOnly"></DragAndDropVue2>
	<div class="mt-8">
		<div
			class="mt-6 mb-2 border-y border-amber-100 text-start"
			v-for="(image, index) in images"
		>
			<div class="py-1 border-y border-neutral-600 text-center">{{image.index + 1}}</div>
			<div class="mb-1 text-red-600">
				id:
				<span
					:class="{
						'text-yellow-800': !image.id,
					}"
				>
					{{ image.id ?? 'null' }}
				</span>
			</div>
			<div class="mb-1">index: {{ image.index }}</div>
			<div class="mb-1">
				src:
				<span
					:class="{
						'text-yellow-800': !image.src,
					}"
				>
					{{ image.src ?? 'null' }}
				</span>
			</div>
			<div class="mb-1">
				file:
				<span
					:class="{
						'text-yellow-800': !image.file,
					}"
				>
					{{ image.file ?? 'null' }}
				</span>
			</div>
		</div>
	</div>
</template>

<script lang="ts" setup>
	import DragAndDrop from './components/DragAndDrop.vue';
	import DragAndDropVue2 from './components/DragAndDropVue2.vue';
	import { onMounted, onUnmounted, ref } from 'vue';
	import { debounce } from 'lodash';

	const images = ref([]);
	const isReadOnly = ref(false);

	onMounted(() => {
		let new_images = [];
		for (let index = 0; index < 5; index++) {
			let { id, src } = getRandom(1, 10, 2, false)
			new_images.push({
				id: id,
				src: src,
				file: null,
			});
		}

		images.value = new_images;
	});
	const getRandomIntInclusive = (min, max) => {
		return Math.floor(Math.random() * (max - min + 1)) + min;
	};
	const getRandomID = () => {
		return Math.floor(Math.random() * (99999999999 - 1 + 1)) + 1;
	};
	const getRandomImage = () => {
		const url_images = ['111', '222', '333', '444', '555'];
		return url_images[getRandomIntInclusive(0, (url_images.length - 1))];
	};
	const isRandom = (min, max, int: number) => {
		return int < getRandomIntInclusive(min, max);
	};
	const getRandom = (min, max, int: number, status = true) => {
		let id = null;
		let src = null;
		if (status && isRandom(min, max, int)) {
			id = getRandomID();
			src = getRandomImage();
		}

		return {
			id,
			src
		}
	};
	/* ===================================  Function to check WebP format support  --Start--  =================================== */
	const testWebP = (callback: (support: boolean) => void) => {
		const webP = new Image();
		webP.onload = webP.onerror = function() {
			// If the image is loaded successfully and its height is 2, then WebP format is supported
			callback(webP.height === 2);
		};
		// Loading an image in WebP format
		webP.src =
			'data:image/webp;base64,UklGRjoAAABXRUJQVlA4IC4AAACyAgCdASoCAAIALmk0mk0iIiIiIgBoSygABc6WWgAA/veff/0PP8bA//LwYAAA';
	};
	const addWebpClass = (support: boolean) => {
		// Determining the class based on WebP format support
		const className = support ? 'webp' : 'no-webp';
		// Adding the class to the root HTML element
		document.documentElement.classList.add(className);
	};
	const checkWebpSupport = () => {
		// Checking WebP format support
		testWebP((support) => {
			// Adding a class based on WebP format support
			addWebpClass(support);
		});
	};
	/* ===================================  Function to check WebP format support  --End--  =================================== */

	/* ===================================  Identify Computer or Mobile  --Start--  =================================== */
	const isMobile = () => {
		const userAgent = navigator.userAgent;
		return {
			Android: () => userAgent.match(/Android/i),
			BlackBerry: () => userAgent.match(/BlackBerry/i),
			iOS: () => userAgent.match(/iPhone|iPad|iPod/i),
			Opera: () => userAgent.match(/Opera Mini/i),
			Windows: () => userAgent.match(/IEMobile/i),
			any: function() {
				const { Android, BlackBerry, iOS, Opera, Windows } = isMobile();
				return Android() || BlackBerry() || iOS() || Opera() || Windows();
			},
		};
	};
	/* ===================================  Identify Computer or Mobile  --End--  =================================== */

	/* ===================================  Function to add _touch or _pc class to HTML element based on device type  --Start--  =================================== */
	const updateHtmlClass = () => {
		const html = document.documentElement;
		const isTouchDevice = isMobile().any();

		html.classList.toggle('_touch', isTouchDevice);
		html.classList.toggle('_pc', !isTouchDevice);
	};
	/* ===================================  Function to add _touch or _pc class to HTML element based on device type  --End--  =================================== */

	/* ===================================  Debounced handler for updateHtmlClass with a delay of 300ms  --Start--  =================================== */
	const debouncedUpdateHtmlClass = debounce(updateHtmlClass, 300);
	/* ===================================  Debounced handler for updateHtmlClass with a delay of 300ms  --End--  =================================== */

	onMounted(() => {
		/* ===================================  Check WebP format support on component mount  --Start--  =================================== */
		checkWebpSupport();
		/* ===================================  Check WebP format support on component mount  --End--  =================================== */
		/* ===================================  Update HTML class on component mount  --Start--  =================================== */
		updateHtmlClass();
		/* ===================================  Update HTML class on component mount  --End--  =================================== */
		/* ===================================  Add resize event listener  --End--  =================================== */
		window.addEventListener('resize', debouncedUpdateHtmlClass);
		/* ===================================  Add resize event listener  --End--  =================================== */
	});

	onUnmounted(() => {
		/* ===================================  Remove resize event listener on component unmount  --Start--  =================================== */
		window.removeEventListener('resize', debouncedUpdateHtmlClass);
		/* ===================================  Remove resize event listener on component unmount  --End--  =================================== */
	});

	/* ====================  Checking the screen resizing  --End--  ==================== */
</script>

<style scoped></style>
