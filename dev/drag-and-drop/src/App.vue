<template>
    <!-- <DragAndDropCategoryVue2></DragAndDropCategoryVue2> -->
    <!-- <DragAndDropCategoryVue3></DragAndDropCategoryVue3> -->
    <DragAndDropVue2
        :images-count="5"
        :is-read-only="isReadOnly"
    ></DragAndDropVue2>
</template>

<script lang="ts" setup>
import DragAndDropCategoryVue2 from './components/DragAndDropCategoryVue2.vue';
import DragAndDropCategoryVue3 from './components/DragAndDropCategoryVue3.vue';
import DragAndDropVue2 from './components/DragAndDropVue2.vue';
import { ref, onMounted, onUnmounted } from 'vue';
import { debounce } from 'lodash';

const isReadOnly = ref(false);

/* ===================================  Function to check WebP format support  --Start--  =================================== */
const testWebP = (callback: (support: boolean) => void) =>  {
    const webP = new Image();
    webP.onload = webP.onerror = function () {
        // If the image is loaded successfully and its height is 2, then WebP format is supported
        callback(webP.height === 2);
    };
    // Loading an image in WebP format
    webP.src = "data:image/webp;base64,UklGRjoAAABXRUJQVlA4IC4AAACyAgCdASoCAAIALmk0mk0iIiIiIgBoSygABc6WWgAA/veff/0PP8bA//LwYAAA";
}
const addWebpClass = (support: boolean) => {
    // Determining the class based on WebP format support
    const className = support ? 'webp' : 'no-webp';
    // Adding the class to the root HTML element
    document.documentElement.classList.add(className);
}
const checkWebpSupport = () => {
    // Checking WebP format support
    testWebP((support) => {
        // Adding a class based on WebP format support
        addWebpClass(support);
    });
}
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
        }
    };
}
/* ===================================  Identify Computer or Mobile  --End--  =================================== */

/* ===================================  Function to add _touch or _pc class to HTML element based on device type  --Start--  =================================== */
const updateHtmlClass = () => {
    const html = document.documentElement;
    const isTouchDevice = isMobile().any();

    html.classList.toggle('_touch', isTouchDevice);
    html.classList.toggle('_pc', !isTouchDevice);
}
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
