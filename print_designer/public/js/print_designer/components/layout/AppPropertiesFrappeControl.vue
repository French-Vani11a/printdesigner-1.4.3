<template>
	<label v-if="field.isLabelled && (!field.icon || !field.icon.onlyIcon)"
		   class="main-label"
		   v-text="field.label"></label>

	<div :ref="(el) => {
      field.frappeControl(el, field.name);
    }"
		 :key="(MainStore.getCurrentElementsValues?.[0]?.id || '') + field.name"
		 class="frappeControl"></div>
</template>

<script setup>
	import { onUnmounted, onBeforeUnmount } from "vue";
	import { useMainStore } from "../../store/MainStore";

	const MainStore = useMainStore();

	const props = defineProps({
		field: {
			type: Object,
			required: true,
		},
	});

	const field = props.field;

	onBeforeUnmount(() => {
		const control = MainStore.frappeControls[field.name];
		if (control?.df?.fieldtype === "Color") {
			// Assuming these are jQuery or similar events; adjust if needed
			control.$wrapper.off("show.bs.popover");
			control.$wrapper.off("hidden.bs.popover");
		}
	});

	onUnmounted(() => {
		const control = MainStore.frappeControls[field.name];
		if (control?.awesomplete) {
			control.awesomplete.destroy();

			if (window.Awesomplete?.all && Array.isArray(window.Awesomplete.all)) {
				const index = window.Awesomplete.all.indexOf(control.awesomplete);
				if (index > -1) {
					window.Awesomplete.all.splice(index, 1);
				}
			}
		}

		delete MainStore.frappeControls[field.name];
	});
</script>

<style lang="scss" scoped>
	.main-label {
		/* Add your label styles here if needed */
	}

	.frappeControl {
		/* Add your control container styles here if needed */
	}
</style>
