<script setup lang="ts">
	import { ref, computed } from "vue";
	import EditorPane from "./components/EditorPane.vue";
	import PreviewPane from "./components/PreviewPane.vue";

	// --- State ---
	const activeTab = ref<"html" | "css" | "javascript">("html");
	const leftWidth = ref(50);

	const html = ref(`<!DOCTYPE html>\n<html>\n\t<body>\n\t\t<h1>Hello, World!</h1>\n\t</body>\n</html>`);
	const css = ref("");
	const js = ref("");

	const activeCode = computed({
		get: () =>
			activeTab.value === "html"
				? html.value
				: activeTab.value === "css"
					? css.value
					: js.value,
		set: (val: string) => {
			if (activeTab.value === "html") html.value = val;
			else if (activeTab.value === "css") css.value = val;
			else js.value = val;
		},
	});

	const srcDoc = computed(
		() => `
  <!DOCTYPE html>
  <html>
    <head><style>${css.value}</style></head>
    <body>
      ${html.value}
      <script>${js.value}<\/script>
    </body>
  </html>
`,
	);
</script>

<template>
	<div class="flex flex-col h-screen w-screen overflow-hidden">
		<header
			class="h-10 bg-gray-900 border-b border-gray-700 flex items-center justify-between px-4"
			role="banner"
		>
			<nav role="tablist" aria-label="Editor languages" class="flex gap-1">
				<button
					v-for="tab in ['html', 'css', 'javascript']"
					:key="tab"
					@click="activeTab = tab as any"
					role="tab"
					:aria-selected="activeTab === tab"
					:class="[
						'px-3 py-1 text-xs uppercase focus:outline-none focus:ring-2 focus:ring-blue-500',
						activeTab === tab
							? 'text-blue-400 border-b-2 border-blue-400'
							: 'text-gray-400',
					]"
				>
					{{ tab }}
				</button>
			</nav>

			<nav aria-label="Layout settings" class="flex gap-2">
				<button
					v-for="size in [30, 50, 70]"
					:key="size"
					@click="leftWidth = size"
					:aria-label="`Set editor width to ${size} percent`"
					class="px-3 py-1 text-xs bg-gray-700 hover:bg-gray-600 text-white rounded transition-colors focus:outline-none focus:ring-2 focus:ring-blue-400"
				>
					{{ size }}/{{ 100 - size }}
				</button>
			</nav>
		</header>

		<main class="flex flex-1 overflow-hidden" role="main">
			<div
				class="bg-gray-900 h-full transition-all duration-300 ease-in-out"
				:style="{ width: leftWidth + '%' }"
			>
				<EditorPane v-model="activeCode" :lang="activeTab" :key="activeTab" />
			</div>

			<div
				class="h-full bg-white transition-all duration-300 ease-in-out"
				:style="{ width: 100 - leftWidth + '%' }"
			>
				<PreviewPane :srcDoc="srcDoc" />
			</div>
		</main>
	</div>
</template>
