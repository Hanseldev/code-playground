<script setup lang="ts">
	defineProps<{
		srcDoc: string;
	}>();
</script>

<template>
	<iframe
		:srcdoc="srcDoc"
		class="w-full h-full border-none"
		sandbox="allow-scripts"
		title="Preview"
	></iframe>
</template>
