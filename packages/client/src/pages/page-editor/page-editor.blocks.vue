<template>
<Sortable :model-value="modelValue" tag="div" item-key="id" handle=".drag-handle" :group="{ name: 'blocks' }" :animation="150" :swap-threshold="0.5" @update:modelValue="v => $emit('update:modelValue', v)">
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
