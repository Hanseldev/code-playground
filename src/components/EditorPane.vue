<script setup lang="ts">
	import { ref, onMounted, onBeforeUnmount, watch } from "vue";
	import { EditorView, basicSetup } from "codemirror";
	import { html } from "@codemirror/lang-html";
	import { css } from "@codemirror/lang-css";
	import { javascript } from "@codemirror/lang-javascript";
    import { oneDark } from "@codemirror/theme-one-dark";

	const props = defineProps<{
		modelValue: string;
		lang: "html" | "css" | "javascript";
	}>();

	const emit = defineEmits(["update:modelValue"]);
	const editorEl = ref<HTMLElement | null>(null);
	let view: EditorView | null = null;

	const getLanguageExtension = (lang: string) => {
		switch (lang) {
			case "html":
				return html();
			case "css":
				return css();
			case "javascript":
				return javascript();
			default:
				return html();
		}
	};

	const initEditor = () => {
		if (!editorEl.value) return;

		view = new EditorView({
			doc: props.modelValue,
			extensions: [
				basicSetup,
                oneDark,
				getLanguageExtension(props.lang),
				EditorView.updateListener.of((update) => {
					if (update.docChanged) {
						emit("update:modelValue", update.state.doc.toString());
					}
				}),
			],
			parent: editorEl.value,
		});
	};

	onMounted(initEditor);

	// Watch for language changes to force re-initialization
	watch(
		() => props.lang,
		() => {
			if (view) {
				view.destroy();
				initEditor();
			}
		},
	);

	onBeforeUnmount(() => {
		if (view) view.destroy();
	});
</script>

<template>
	<div ref="editorEl" class="h-full w-full overscroll-y-none text-sm"></div>
</template>
