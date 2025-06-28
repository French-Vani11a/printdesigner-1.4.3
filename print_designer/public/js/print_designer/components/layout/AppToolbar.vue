<template>
	<div class="sidebar">
		<div class="toolbar-section mt-3">
			<div>
				<template v-for="(control, key) in MainStore.controls" :key="control.id">
					<span v-if="!control.isDisabled"
						  :title="control.aria_label"
						  :class="iconClasses(control.id, control.icon)"
						  @click="MainStore.setActiveControl(key)">
						<svg viewBox="0 0 24 24" width="16" height="16">
							<use :href="`#${control.icon}`"
								 :style="{ color: MainStore.activeControl === control.id ? 'var(--primary)' : '' }" />
						</svg>
					</span>
				</template>
			</div>
			<IconsUse name="layerPanel"
					  key="layerPanel"
					  :size="44"
					  :padding="14"
					  :color="MainStore.isLayerPanelEnabled ? 'var(--primary-color)' : 'var(--gray-800)'"
					  :custom-class="MainStore.isLayerPanelEnabled ? 'active-tool-icon' : ''"
					  @click="MainStore.isLayerPanelEnabled = !MainStore.isLayerPanelEnabled" />
		</div>

		<LayersPanel v-if="MainStore.isLayerPanelEnabled" />
	</div>
</template>

<script setup>
	import IconsUse from "../../icons/IconsUse.vue";
	import { useMainStore } from "../../store/MainStore";
	import LayersPanel from "./LayersPanel.vue";

	const MainStore = useMainStore();

	const iconClasses = (id, icon) => [
		icon,
		"tool-icons",
		{ "active-tool-icon": MainStore.activeControl === id },
	];
</script>

<style scoped>
	.sidebar {
		display: flex;
	}

	.toolbar-section {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}

	.tool-icons {
		display: flex;
		align-items: center;
		justify-content: center;
		user-select: none;
		cursor: pointer;
		width: 32px;
		height: 32px;
		margin: 6px;
	}

	.active-layer-icon {
		cursor: pointer;
		--icon-stroke: #f9f3e6;
		border-radius: var(--border-radius-sm);
		background-color: var(--primary);
		margin: 6px;
	}

	.active-tool-icon {
		background-color: var(--primary);
		--icon-stroke: #f9f3e6;
		border-radius: var(--border-radius-sm);
	}

	.tool-icons:hover:not(.active-tool-icon) {
		border-radius: var(--border-radius-md);
		--icon-stroke: #f9f3e6;
		background-color: var(--primary);
	}
</style>
