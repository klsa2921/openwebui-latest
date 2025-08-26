<script>
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';
	import { functions } from '$lib/stores';

	import { getFunctions } from '$lib/apis/functions';
	import { config } from '$lib/stores';
	import Functions from '$lib/components/admin/Functions.svelte';

	let functionsEnabled = true;
	let loaded = false;

	onMount(async () => {
		// Check if functions are enabled from config store
		if ($config?.features && !$config.features.enable_admin_functions) {
			functionsEnabled = false;
			await goto('/admin');
			return;
		}

		try {
			await Promise.all([
				(async () => {
					functions.set(await getFunctions(localStorage.token));
				})()
			]);
		} catch (error) {
			console.error('Failed to load functions:', error);
		}
		loaded = true;
	});
</script>

{#if loaded && functionsEnabled && $functions !== null}
	<Functions />
{:else if loaded && !functionsEnabled}
	<div class="flex justify-center items-center h-64">
		<div class="text-center">
			<h2 class="text-xl font-semibold mb-2">Functions Disabled</h2>
			<p class="text-gray-600 dark:text-gray-400">Functions management has been disabled by the administrator.</p>
		</div>
	</div>
{/if}
