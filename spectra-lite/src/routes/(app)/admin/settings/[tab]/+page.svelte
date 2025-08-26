<script>
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';
	import { config } from '$lib/stores';
	import Settings from '$lib/components/admin/Settings.svelte';

	let settingsEnabled = true;
	let loaded = false;

	onMount(async () => {
		// Check if settings are enabled from config store
		if ($config?.features && !$config.features.enable_admin_settings) {
			settingsEnabled = false;
			await goto('/admin');
			return;
		}
		loaded = true;
	});
</script>

{#if loaded && settingsEnabled}
	<Settings />
{:else if loaded && !settingsEnabled}
	<div class="flex justify-center items-center h-64">
		<div class="text-center">
			<h2 class="text-xl font-semibold mb-2">Settings Disabled</h2>
			<p class="text-gray-600 dark:text-gray-400">Settings management has been disabled by the administrator.</p>
		</div>
	</div>
{/if}
