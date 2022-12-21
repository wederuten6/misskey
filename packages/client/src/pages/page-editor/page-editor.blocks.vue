<template>
<XDraggable v-model="blocks" tag="div" item-key="id" handle=".drag-handle" :group="{ name: 'blocks' }" animation="150" swap-threshold="0.5">
	<template #item="{element}">
		<component :is="'x-' + element.type" :model-value="element" @update:modelValue="updateItem" @remove="() => removeItem(element)"/>
	</template>
</XDraggable>
</template>

<script lang="ts">
import { defineComponent, defineAsyncComponent } from 'vue';
import XSection from './els/page-editor.el.section.vue';
import XText from './els/page-editor.el.text.vue';
import XImage from './els/page-editor.el.image.vue';
import XNote from './els/page-editor.el.note.vue';
import * as os from '@/os';

export default defineComponent({
	components: {
		XDraggable: defineAsyncComponent(() => import('vuedraggable').then(x => x.default)),
		XSection, XText, XImage, XNote,
	},

	props: {
		modelValue: {
			type: Array,
			required: true,
		},
	},

	emits: ['update:modelValue'],

	computed: {
		blocks: {
			get() {
				return this.modelValue;
			},
			set(value) {
				this.$emit('update:modelValue', value);
			},
		},
	},

	methods: {
		updateItem(v) {
			const i = this.blocks.findIndex(x => x.id === v.id);
			const newValue = [
				...this.blocks.slice(0, i),
				v,
				...this.blocks.slice(i + 1),
			];
			this.$emit('update:modelValue', newValue);
		},

		removeItem(el) {
			const i = this.blocks.findIndex(x => x.id === el.id);
			const newValue = [
				...this.blocks.slice(0, i),
				...this.blocks.slice(i + 1),
			];
			this.$emit('update:modelValue', newValue);
		},
	},
});
</script>
