<script setup lang="ts">
	import { ref, onMounted, onBeforeUnmount, watch } from "vue";
	import { EditorView, basicSetup } from "codemirror";
	import { html } from "@codemirror/lang-html";
	import { css } from "@codemirror/lang-css";
	import { javascript } from "@codemirror/lang-javascript";
	import { oneDark } from "@codemirror/theme-one-dark";

	import { keymap } from "@codemirror/view";
	import { indentWithTab } from "@codemirror/commands";
    
	// Linting utilities
	import { lintGutter, linter } from "@codemirror/lint";
	import type { Diagnostic } from "@codemirror/lint";

	const props = defineProps<{
		modelValue: string;
		lang: "html" | "css" | "javascript";
	}>();

	const emit = defineEmits(["update:modelValue"]);
	const editorEl = ref<HTMLElement | null>(null);
	let view: EditorView | null = null;

	const simpleLinter = linter((view) => {
		const diagnostics: Diagnostic[] = [];
		if (props.lang === "javascript") {
			const text = view.state.doc.toString();

			if (text.includes("var ")) {
				diagnostics.push({
					from: 0,
					to: text.length,
					severity: "warning",
					message: "Prefer 'const' or 'let' over 'var'",
				});
			}
		}
		return diagnostics;
	});

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

				// this makes the tab behavior insert 2 spaces instead of focusing the next element
				keymap.of([indentWithTab]),

				// Add lintng to extensions
				lintGutter(),
				simpleLinter,
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
	<div
		ref="editorEl"
		class="h-full w-full overscroll-y-none text-sm"
		role="region"
		aria-label="Code Editor"
	></div>
</template>
