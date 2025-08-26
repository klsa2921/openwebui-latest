<script>
	import { goto } from '$app/navigation';
	import { onMount } from 'svelte';

	import { config } from '$lib/stores';
	import Evaluations from '$lib/components/admin/Evaluations.svelte';

	let evaluationsEnabled = true;
	let loaded = false;

	onMount(async () => {
		// Check if evaluations are enabled from config store
		if ($config?.features && !$config.features.enable_admin_evaluations) {
			evaluationsEnabled = false;
			await goto('/admin');
			return;
		}

		await goto('/admin/evaluations/leaderboard');
		loaded = true;
	});
</script>

{#if loaded && evaluationsEnabled}
	<Evaluations />
{:else if loaded && !evaluationsEnabled}
	<div class="flex justify-center items-center h-64">
		<div class="text-center">
			<h2 class="text-xl font-semibold mb-2">Evaluations Disabled</h2>
			<p class="text-gray-600 dark:text-gray-400">Evaluations management has been disabled by the administrator.</p>
		</div>
	</div>
{/if}
