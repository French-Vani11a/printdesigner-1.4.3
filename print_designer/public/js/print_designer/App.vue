<template>
	<div>
		<link rel="preconnect" href="https://fonts.gstatic.com" />
		<link v-for="currentFont in MainStore.currentFonts"
			  :key="currentFont"
			  :href="`https://fonts.googleapis.com/css2?family=${currentFont}:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap`"
			  rel="stylesheet" />
		<link rel="preload"
			  href="/assets/print_designer/images/mouse-pointer.svg"
			  as="image"
			  type="image/svg+xml" />
		<link rel="preload"
			  href="/assets/print_designer/images/add-text.svg"
			  as="image"
			  type="image/svg+xml" />
		<link rel="preload"
			  href="/assets/print_designer/images/add-image.svg"
			  as="image"
			  type="image/svg+xml" />
		<link rel="preload"
			  href="/assets/print_designer/images/add-table.svg"
			  as="image"
			  type="image/svg+xml" />
		<link rel="preload"
			  href="/assets/print_designer/images/add-rectangle.svg"
			  as="image"
			  type="image/svg+xml" />
		<AppHeader :print_format_name="print_format_name" />
		<div class="main-layout" id="main-layout">
			<AppToolbar :class="toolbarClasses" />
			<AppCanvas class="app-sections print-format-container" />
			<AppPropertiesPanel class="app-sections properties-panel" />
		</div>
	</div>
</template>

<script setup>
import { computed, onMounted, watchEffect } from "vue";
import { useMainStore } from "./store/MainStore";
import AppHeader from "./components/layout/AppHeader.vue";
import AppToolbar from "./components/layout/AppToolbar.vue";
import AppCanvas from "./components/layout/AppCanvas.vue";
import AppPropertiesPanel from "./components/layout/AppPropertiesPanel.vue";
import { useAttachKeyBindings } from "./composables/AttachKeyBindings";
import { fetchMeta } from "./store/fetchMetaAndData";

const props = defineProps({
	print_format_name: {
		type: String,
		required: true,
	},
});
const MainStore = useMainStore();

const toolbarClasses = computed(() => {
	return [
		"app-sections",
		"toolbar",
		{ "toolbar-with-layer-panel": MainStore.isLayerPanelEnabled },
	];
});

useAttachKeyBindings();
onMounted(() => {
	MainStore.printDesignName = props.print_format_name;
	fetchMeta();
	const screen_stylesheet = document.createElement("style");
	screen_stylesheet.title = "print-designer-stylesheet";
	screen_stylesheet.rel = "stylesheet";
	document.head.appendChild(screen_stylesheet);

	const print_stylesheet = document.createElement("style");
	print_stylesheet.title = "page-print-designer-stylesheet";
	print_stylesheet.rel = "stylesheet";
	print_stylesheet.media = "page";
	document.head.appendChild(print_stylesheet);
	for (let i = document.styleSheets.length - 1; i >= 0; i--) {
		if (document.styleSheets[i].title == "print-designer-stylesheet") {
			MainStore.screenStyleSheet = document.styleSheets[i];
		} else if (document.styleSheets[i].title == "page-print-designer-stylesheet") {
			MainStore.printStyleSheet = document.styleSheets[i];
		}
	}
});
watchEffect(() => {
	if (MainStore.activeControl == "mouse-pointer") {
		MainStore.isMarqueeActive = true;
		MainStore.isDrawing = false;
	} else if (["rectangle", "image", "table", "barcode"].includes(MainStore.activeControl)) {
		MainStore.isMarqueeActive = false;
		MainStore.isDrawing = true;
	} else {
		MainStore.isMarqueeActive = false;
		MainStore.isDrawing = false;
	}
});
</script>
<style deep lang="scss">
.main-layout {
	display: flex;
	justify-content: space-between;
	margin: 0;
	cursor: default;
	--primary: #7b4b57;
	--primary-color: #7b4b57;
	.app-sections {
		flex: 1;
		height: calc(100vh - var(--navbar-height));
		padding: 0;
		margin-top: 0;
		background-color: var(--card-bg);
		box-shadow: var(--card-shadow);
	}

	.toolbar {
		z-index: 1;
		width: 44px;
		max-width: 44px;
	}
	.toolbar-with-layer-panel {
		width: 244px;
		max-width: 244px;
		box-shadow: unset;
	}
	.print-format-container {
		overflow: auto;
		display: flex;
		position: relative;
		flex-direction: column;
		height: calc(100vh - var(--navbar-height));
		background-color: var(--subtle-fg);
	}
	.properties-panel {
		width: 250px;
		max-width: 250px;
	}
}
</style>
