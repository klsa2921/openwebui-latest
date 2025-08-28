<script lang="ts">
	import { getContext } from 'svelte';
	import Textarea from '$lib/components/common/Textarea.svelte';
	import Tooltip from '$lib/components/common/Tooltip.svelte';

	const i18n = getContext('i18n');

	export let name = '';
	export let color = '';
	export let description = '';
</script>

<div class="flex flex-col gap-4 w-full">
  <!-- Name -->
  <div class="flex flex-col w-full">
    <label class="mb-1 text-xs font-medium text-gray-600 dark:text-gray-300">
      {$i18n.t('Name')}
    </label>
    <input
      type="text"
      bind:value={name}
      placeholder={$i18n.t('Group Name')}
      autocomplete="off"
      required
      class="w-full text-sm px-3 py-2 rounded-lg border border-gray-300 dark:border-gray-700 
             bg-white dark:bg-gray-900 shadow-sm focus:ring-2 focus:ring-blue-400 
             dark:focus:ring-blue-600 transition outline-none placeholder:text-gray-400 dark:placeholder:text-gray-500"
    />
  </div>

  <!-- Color (optional) -->
  <!--
  <div class="flex flex-col w-full">
    <label class="mb-1 text-xs font-medium text-gray-600 dark:text-gray-300">
      {$i18n.t('Color')}
    </label>
    <Tooltip content={$i18n.t('Hex Color - Leave empty for default color')} placement="top-start">
      <div class="flex gap-1">
        <span class="self-center text-gray-500">#</span>
        <input
          type="text"
          bind:value={color}
          placeholder={$i18n.t('Hex Color')}
          autocomplete="off"
          class="w-full text-sm px-3 py-2 rounded-lg border border-gray-300 dark:border-gray-700
                 bg-white dark:bg-gray-900 shadow-sm focus:ring-2 focus:ring-blue-400 
                 dark:focus:ring-blue-600 transition outline-none placeholder:text-gray-400 dark:placeholder:text-gray-500"
        />
      </div>
    </Tooltip>
  </div>
  -->

  <!-- Description -->
  <div class="flex flex-col w-full">
    <label class="mb-1 text-xs font-medium text-gray-600 dark:text-gray-300">
      {$i18n.t('Description')}
    </label>
    <Textarea
      className="w-full text-sm px-3 py-2 rounded-lg border border-gray-300 dark:border-gray-700
                 bg-white dark:bg-gray-900 shadow-sm focus:ring-2 focus:ring-blue-400
                 dark:focus:ring-blue-600 transition outline-none resize-none placeholder:text-gray-400 dark:placeholder:text-gray-500"
      rows={4}
      bind:value={description}
      placeholder={$i18n.t('Group Description')}
    />
  </div>
</div>
