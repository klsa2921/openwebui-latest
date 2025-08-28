<script lang="ts">
	import { getContext, onMount } from 'svelte';
	const i18n = getContext('i18n');

	import Switch from '$lib/components/common/Switch.svelte';
	import Tooltip from '$lib/components/common/Tooltip.svelte';

	// Default values for permissions
	const defaultPermissions = {
		workspace: {
			models: false,
			knowledge: false,
			prompts: false,
			tools: false
		},
		sharing: {
			public_models: false,
			public_knowledge: false,
			public_prompts: false,
			public_tools: false
		},
		chat: {
			controls: true,
			file_upload: true,
			delete: true,
			edit: true,
			share: true,
			export: true,
			stt: true,
			tts: true,
			call: true,
			multiple_models: true,
			temporary: true,
			temporary_enforced: false
		},
		features: {
			direct_tool_servers: false,
			web_search: true,
			image_generation: true,
			code_interpreter: true,
			notes: true
		}
	};

	export let permissions = {};

	// Reactive statement to ensure all fields are present in `permissions`
	$: {
		permissions = fillMissingProperties(permissions, defaultPermissions);
	}

	function fillMissingProperties(obj: any, defaults: any) {
		return {
			...defaults,
			...obj,
			workspace: { ...defaults.workspace, ...obj.workspace },
			sharing: { ...defaults.sharing, ...obj.sharing },
			chat: { ...defaults.chat, ...obj.chat },
			features: { ...defaults.features, ...obj.features }
		};
	}

	onMount(() => {
		permissions = fillMissingProperties(permissions, defaultPermissions);
	});
</script>

<div>
	<!-- <div>
		<div class=" mb-2 text-sm font-medium">{$i18n.t('Model Permissions')}</div>

		<div class="mb-2">
			<div class="flex justify-between items-center text-xs pr-2">
				<div class=" text-xs font-medium">{$i18n.t('Model Filtering')}</div>

				<Switch bind:state={permissions.model.filter} />
			</div>
		</div>

		{#if permissions.model.filter}
			<div class="mb-2">
				<div class=" space-y-1.5">
					<div class="flex flex-col w-full">
						<div class="mb-1 flex justify-between">
							<div class="text-xs text-gray-500">{$i18n.t('Model IDs')}</div>
						</div>

						{#if model_ids.length > 0}
							<div class="flex flex-col">
								{#each model_ids as modelId, modelIdx}
									<div class=" flex gap-2 w-full justify-between items-center">
										<div class=" text-sm flex-1 rounded-lg">
											{modelId}
										</div>
										<div class="shrink-0">
											<button
												type="button"
												on:click={() => {
													model_ids = model_ids.filter((_, idx) => idx !== modelIdx);
												}}
											>
												<Minus strokeWidth="2" className="size-3.5" />
											</button>
										</div>
									</div>
								{/each}
							</div>
						{:else}
							<div class="text-gray-500 text-xs text-center py-2 px-10">
								{$i18n.t('No model IDs')}
							</div>
						{/if}
					</div>
				</div>
				<hr class=" border-gray-100 dark:border-gray-700/10 mt-2.5 mb-1 w-full" />

				<div class="flex items-center">
					<select
						class="w-full py-1 text-sm rounded-lg bg-transparent {selectedModelId
							? ''
							: 'text-gray-500'} placeholder:text-gray-300 dark:placeholder:text-gray-700 outline-hidden"
						bind:value={selectedModelId}
					>
						<option value="">{$i18n.t('Select a model')}</option>
						{#each $models.filter((m) => m?.owned_by !== 'arena') as model}
							<option value={model.id} class="bg-gray-50 dark:bg-gray-700">{model.name}</option>
						{/each}
					</select>

					<div>
						<button
							type="button"
							on:click={() => {
								if (selectedModelId && !permissions.model.model_ids.includes(selectedModelId)) {
									permissions.model.model_ids = [...permissions.model.model_ids, selectedModelId];
									selectedModelId = '';
								}
							}}
						>
							<Plus className="size-3.5" strokeWidth="2" />
						</button>
					</div>
				</div>
			</div>
		{/if}

		<div class=" space-y-1 mb-3">
			<div class="">
				<div class="flex justify-between items-center text-xs">
					<div class=" text-xs font-medium">{$i18n.t('Default Model')}</div>
				</div>
			</div>

			<div class="flex-1 mr-2">
				<select
					class="w-full bg-transparent outline-hidden py-0.5 text-sm"
					bind:value={permissions.model.default_id}
					placeholder="Select a model"
				>
					<option value="" disabled selected>{$i18n.t('Select a model')}</option>
					{#each permissions.model.filter ? $models.filter( (model) => filterModelIds.includes(model.id) ) : $models.filter((model) => model.id) as model}
						<option value={model.id} class="bg-gray-100 dark:bg-gray-700">{model.name}</option>
					{/each}
				</select>
			</div>
		</div>
	</div>

	<hr class=" border-gray-100 dark:border-gray-850 my-2" /> -->

<div class="flex flex-col gap-4 w-full">

  <!-- Workspace Permissions -->
  <div class="p-4 rounded-lg border border-gray-200 dark:border-gray-700 shadow-sm bg-white dark:bg-gray-900 transition">
    <div class="mb-2 text-sm font-medium text-gray-700 dark:text-gray-200">
      {$i18n.t('Workspace Permissions')}
    </div>

    <div class="flex flex-col gap-3">
      <div class="flex justify-between items-center p-2 rounded-md hover:bg-gray-50 dark:hover:bg-gray-800 transition">
        <div class="text-xs font-medium text-gray-600 dark:text-gray-300">{$i18n.t('Models Access')}</div>
        <Switch bind:state={permissions.workspace.models} />
      </div>

      <div class="flex justify-between items-center p-2 rounded-md hover:bg-gray-50 dark:hover:bg-gray-800 transition">
        <div class="text-xs font-medium text-gray-600 dark:text-gray-300">{$i18n.t('Knowledge Access')}</div>
        <Switch bind:state={permissions.workspace.knowledge} />
      </div>

      <div class="flex justify-between items-center p-2 rounded-md hover:bg-gray-50 dark:hover:bg-gray-800 transition">
        <div class="text-xs font-medium text-gray-600 dark:text-gray-300">{$i18n.t('Prompts Access')}</div>
        <Switch bind:state={permissions.workspace.prompts} />
      </div>

      <Tooltip
        className="flex justify-between items-center p-2 rounded-md hover:bg-gray-50 dark:hover:bg-gray-800 transition"
        content={$i18n.t('Warning: Enabling this will allow users to upload arbitrary code on the server.')}
        placement="top-start"
      >
        <div class="text-xs font-medium text-gray-600 dark:text-gray-300">{$i18n.t('Tools Access')}</div>
        <Switch bind:state={permissions.workspace.tools} />
      </Tooltip>
    </div>
  </div>

  <hr class="border-gray-100 dark:border-gray-850" />

  <!-- Sharing Permissions -->
  <div class="p-4 rounded-lg border border-gray-200 dark:border-gray-700 shadow-sm bg-white dark:bg-gray-900 transition">
    <div class="mb-2 text-sm font-medium text-gray-700 dark:text-gray-200">{$i18n.t('Sharing Permissions')}</div>

    <div class="flex flex-col gap-3">
      <div class="flex justify-between items-center p-2 rounded-md hover:bg-gray-50 dark:hover:bg-gray-800 transition">
        <div class="text-xs font-medium text-gray-600 dark:text-gray-300">{$i18n.t('Models Public Sharing')}</div>
        <Switch bind:state={permissions.sharing.public_models} />
      </div>

      <div class="flex justify-between items-center p-2 rounded-md hover:bg-gray-50 dark:hover:bg-gray-800 transition">
        <div class="text-xs font-medium text-gray-600 dark:text-gray-300">{$i18n.t('Knowledge Public Sharing')}</div>
        <Switch bind:state={permissions.sharing.public_knowledge} />
      </div>

      <div class="flex justify-between items-center p-2 rounded-md hover:bg-gray-50 dark:hover:bg-gray-800 transition">
        <div class="text-xs font-medium text-gray-600 dark:text-gray-300">{$i18n.t('Prompts Public Sharing')}</div>
        <Switch bind:state={permissions.sharing.public_prompts} />
      </div>

      <div class="flex justify-between items-center p-2 rounded-md hover:bg-gray-50 dark:hover:bg-gray-800 transition">
        <div class="text-xs font-medium text-gray-600 dark:text-gray-300">{$i18n.t('Tools Public Sharing')}</div>
        <Switch bind:state={permissions.sharing.public_tools} />
      </div>
    </div>
  </div>

  <hr class="border-gray-100 dark:border-gray-850" />

  <!-- Chat Permissions -->
  <div class="p-4 rounded-lg border border-gray-200 dark:border-gray-700 shadow-sm bg-white dark:bg-gray-900 transition">
    <div class="mb-2 text-sm font-medium text-gray-700 dark:text-gray-200">{$i18n.t('Chat Permissions')}</div>

    <div class="flex flex-col gap-3">
      {#each Object.entries(permissions.chat) as [key, value]}
        <div class="flex justify-between items-center p-2 rounded-md hover:bg-gray-50 dark:hover:bg-gray-800 transition">
          <div class="text-xs font-medium text-gray-600 dark:text-gray-300">
            {$i18n.t(key.replace(/_/g, ' ').replace(/\b\w/g, c => c.toUpperCase()))}
          </div>
          <Switch bind:state={permissions.chat[key]} />
        </div>
      {/each}
    </div>
  </div>

  <hr class="border-gray-100 dark:border-gray-850" />

  <!-- Features Permissions -->
  <div class="p-4 rounded-lg border border-gray-200 dark:border-gray-700 shadow-sm bg-white dark:bg-gray-900 transition">
    <div class="mb-2 text-sm font-medium text-gray-700 dark:text-gray-200">{$i18n.t('Features Permissions')}</div>

    <div class="flex flex-col gap-3">
      {#each Object.entries(permissions.features) as [key, value]}
        <div class="flex justify-between items-center p-2 rounded-md hover:bg-gray-50 dark:hover:bg-gray-800 transition">
          <div class="text-xs font-medium text-gray-600 dark:text-gray-300">
            {$i18n.t(key.replace(/_/g, ' ').replace(/\b\w/g, c => c.toUpperCase()))}
          </div>
          <Switch bind:state={permissions.features[key]} />
        </div>
      {/each}
    </div>
  </div>

</div>

</div>
