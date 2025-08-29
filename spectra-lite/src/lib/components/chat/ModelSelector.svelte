<script lang="ts">
	import { models, showSettings, settings, user, mobile, config } from '$lib/stores';
	import { getContext } from 'svelte';
	import { toast } from 'svelte-sonner';
	import { updateUserSettings } from '$lib/apis/users';
    import Selector from './ModelSelector/Selector.svelte';
    import TemporaryChatToggle from './ModelSelector/TemporaryChatToggle.svelte';
	const i18n = getContext('i18n');

	export let selectedModels = [''];
	export let disabled = false;
	export let showSetDefault = true;

	// Optional: Track temporary mode per model (by index)
	let temporaryChat = selectedModels.map(() => false);

	const saveDefaultModel = async () => {
		const hasEmptyModel = selectedModels.filter((it) => it === '');
		if (hasEmptyModel.length) {
			toast.error($i18n.t('Choose a model before saving...'));
			return;
		}
		settings.set({ ...$settings, models: selectedModels });
		await updateUserSettings(localStorage.token, { ui: $settings });

		toast.success($i18n.t('Default model updated'));
	};

	const toggleModel = (modelId: string, index: number) => {
		selectedModels[index] = modelId;
		selectedModels = [...selectedModels]; // trigger reactivity
	};

	const toggleTemporary = (index: number) => {
		temporaryChat[index] = !temporaryChat[index];
		temporaryChat = [...temporaryChat];
	};
</script>

<div class="flex flex-col w-full items-start gap-3">
	
	{#each selectedModels as selectedModel, index}
		<div class="w-full">
			<div class="flex flex-row items-center">
			<div class="font-medium mb-1 text-base text-gray-700 dark:text-gray-300">
				{$i18n.t('Select a model')} {selectedModels.length > 1 ? `#${index + 1}` : ''}
			</div>
			<div class="text-left ml-1 text-[0.7rem] text-gray-600 dark:text-gray-400 font-primary">
		<button on:click={saveDefaultModel} class="underline">
			({$i18n.t('Set as default')})
		</button>
	</div>
	</div>
			<div class="flex flex-wrap gap-2">
				{#each $models as model, index}
				{#if model?.id !== "arena-model"}
					<button
					class="px-3 py-2 text-sm rounded-md border font-medium text-left transition-all
						hover:bg-blue-50 dark:hover:bg-gray-800
						{selectedModel === model.id
						? 'bg-blue-100 dark:bg-gray-700 border-blue-500 dark:border-blue-400 text-blue-900 dark:text-blue-200'
						: 'bg-white dark:bg-gray-900 border-gray-300 dark:border-gray-700 text-gray-700 dark:text-gray-200'}"
					on:click={() => toggleModel(model.id, index)}
					>
					{model.name}
					</button>
				{/if}
				{/each}

			</div>

	<!-- Temporary chat switch -->

{#if $user?.role === 'user'
  ? ($user?.permissions?.chat?.temporary ?? true) &&
    !($user?.permissions?.chat?.temporary_enforced ?? false)
  : true}
  <TemporaryChatToggle />
{/if}




			<!-- Add/remove model buttons
			{#if $user?.role === 'admin' || ($user?.permissions?.chat?.multiple_models ?? true)}
				<div class="mt-2 flex gap-2 items-center">
					{#if index === 0}
						<Tooltip content={$i18n.t('Add Model')}>
							<button
								on:click={() => {
									selectedModels = [...selectedModels, ''];
									temporaryChat = [...temporaryChat, false];
								}}
								class="text-sm text-gray-600 dark:text-gray-300 hover:text-blue-500"
							>
								+ {$i18n.t('Add')}
							</button>
						</Tooltip>
					{:else}
						<Tooltip content={$i18n.t('Remove Model')}>
							<button
								on:click={() => {
									selectedModels.splice(index, 1);
									temporaryChat.splice(index, 1);
									selectedModels = [...selectedModels];
									temporaryChat = [...temporaryChat];
								}}
								class="text-sm text-red-600 hover:text-red-400"
							>
								– {$i18n.t('Remove')}
							</button>
						</Tooltip>
					{/if} 
				</div>
			{/if} -->
		</div>
	{/each}
</div>

<!-- Set as default -->

	
