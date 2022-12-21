<template>
<Sortable :list="modelValue" tag="div" item-key="id" :options="{ handle: '.drag-handle', group: { name: 'blocks' }, animation: 150, swapThreshold: 0.5 }" @end="onSorted">
	<template #item="{element}">
		<component :is="'x-' + element.type" :model-value="element" @update:modelValue="updateItem" @remove="() => removeItem(element)"/>
	</template>
</Sortable>
</template>

<script lang="ts">
import { defineComponent, defineAsyncComponent } from 'vue';
import XSection from './els/page-editor.el.section.vue';
import XText from './els/page-editor.el.text.vue';
import XImage from './els/page-editor.el.image.vue';
import XNote from './els/page-editor.el.note.vue';
import * as os from '@/os';
import { deepClone } from '@/scripts/clone';

export default defineComponent({
	components: {
		Sortable: defineAsyncComponent(() => import('vuedraggable').then(x => x.default)),
		XSection, XText, XImage, XNote,
	},

	props: {
		modelValue: {
			type: Array,
			required: true,
		},
	},

	emits: ['update:modelValue'],

	methods: {
		onSorted(event) {
			const items = deepClone(this.modelValue);
			const item = items.splice(event.oldIndex, 1)[0];
			items.splice(event.newIndex, 0, item);
			this.$emit('update:modelValue', items);
		},

		updateItem(v) {
			const i = this.modelValue.findIndex(x => x.id === v.id);
			const newValue = [
				...this.modelValue.slice(0, i),
				v,
				...this.modelValue.slice(i + 1),
			];
			this.$emit('update:modelValue', newValue);
		},

		removeItem(el) {
			const i = this.modelValue.findIndex(x => x.id === el.id);
			const newValue = [
				...this.modelValue.slice(0, i),
				...this.modelValue.slice(i + 1),
			];
			this.$emit('update:modelValue', newValue);
		},
	},
});
</script>
