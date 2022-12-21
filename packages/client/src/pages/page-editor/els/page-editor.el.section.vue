<template>
<!-- eslint-disable vue/no-mutating-props -->
<XContainer :draggable="true" @remove="() => $emit('remove')">
	<template #header><i class="fas fa-sticky-note"></i> {{ props.modelValue.title }}</template>
	<template #func>
		<button class="_button" @click="rename()">
			<i class="ti ti-pencil"></i>
		</button>
		<button class="_button" @click="add()">
			<i class="ti ti-plus"></i>
		</button>
	</template>

	<section class="ilrvjyvi">
		<XBlocks v-model="children" class="children"/>
	</section>
</XContainer>
</template>

<script lang="ts" setup>
/* eslint-disable vue/no-mutating-props */
import { defineAsyncComponent, inject, onMounted, watch } from 'vue';
import { v4 as uuid } from 'uuid';
import XContainer from '../page-editor.container.vue';
import * as os from '@/os';
import { i18n } from '@/i18n';

const XBlocks = defineAsyncComponent(() => import('../page-editor.blocks.vue'));

const props = withDefaults(defineProps<{
	modelValue: any,
}>(), {
	modelValue: {},
});

const emit = defineEmits<{
	(ev: 'update:modelValue', value: any): void;
}>();

const children = $ref(props.modelValue.children ?? []);

watch(children, () => {
	emit('update:modelValue', {
		...props.modelValue,
		children,
	});
});

const getPageBlockList = inject<(any) => any>('getPageBlockList');

async function rename() {
	const { canceled, result: title } = await os.inputText({
		title: 'Enter title',
		default: props.modelValue.title,
	});
	if (canceled) return;
	emit('update:modelValue', {
		...props.modelValue,
		title,
	});
}

async function add() {
	const { canceled, result: type } = await os.select({
		title: i18n.ts._pages.chooseBlock,
		items: getPageBlockList(),
	});
	if (canceled) return;

	const id = uuid();
	children.push({ id, type });
}

onMounted(() => {
	if (props.modelValue.title == null) {
		rename();
	}
});
</script>

<style lang="scss" scoped>
.ilrvjyvi {
	> .children {
		padding: 16px;
	}
}
</style>
