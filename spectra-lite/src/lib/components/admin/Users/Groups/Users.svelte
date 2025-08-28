<script lang="ts">
	import { getContext } from 'svelte';
	const i18n = getContext('i18n');

	import Tooltip from '$lib/components/common/Tooltip.svelte';
	import Plus from '$lib/components/icons/Plus.svelte';
	import { WEBUI_BASE_URL } from '$lib/constants';
	import Checkbox from '$lib/components/common/Checkbox.svelte';
	import Badge from '$lib/components/common/Badge.svelte';
	import Search from '$lib/components/icons/Search.svelte';

	export let users = [];
	export let userIds = [];

	let filteredUsers = [];

	$: filteredUsers = users
		.filter((user) => {
			if (query === '') {
				return true;
			}

			return (
				user.name.toLowerCase().includes(query.toLowerCase()) ||
				user.email.toLowerCase().includes(query.toLowerCase())
			);
		})
		.sort((a, b) => {
			const aUserIndex = userIds.indexOf(a.id);
			const bUserIndex = userIds.indexOf(b.id);

			// Compare based on userIds or fall back to alphabetical order
			if (aUserIndex !== -1 && bUserIndex === -1) return -1; // 'a' has valid userId -> prioritize
			if (bUserIndex !== -1 && aUserIndex === -1) return 1; // 'b' has valid userId -> prioritize

			// Both a and b are either in the userIds array or not, so we'll sort them by their indices
			if (aUserIndex !== -1 && bUserIndex !== -1) return aUserIndex - bUserIndex;

			// If both are not in the userIds, fallback to alphabetical sorting by name
			return a.name.localeCompare(b.name);
		});

	let query = '';
</script>

<div class="flex flex-col gap-4 w-full">

  <!-- Search Input -->
  <div class="flex w-full">
    <div
      class="flex flex-1 items-center px-4 py-2
             border border-gray-200 dark:border-gray-700
             rounded-xl bg-white dark:bg-gray-900
             shadow-sm focus-within:ring-2 focus-within:ring-blue-500 transition"
    >
      <!-- Icon -->
      <div class="text-gray-400 dark:text-gray-500 mr-2">
        <Search className="size-4" />
      </div>

      <!-- Input -->
      <input
        class="w-full text-sm bg-transparent outline-none 
               placeholder-gray-400 dark:placeholder-gray-500 
               text-gray-800 dark:text-gray-200"
        bind:value={query}
        placeholder={$i18n.t('Search')}
      />
    </div>
  </div>

  <!-- Users List -->
  <div class="max-h-[22rem] overflow-y-auto scrollbar-hidden flex flex-col gap-2.5">
    {#if filteredUsers.length > 0}
      {#each filteredUsers as user (user.id)}
        <div
          class="flex flex-row items-center gap-3 w-full p-2 rounded-lg
                 border border-gray-100 dark:border-gray-800
                 bg-white dark:bg-gray-900 shadow-sm
                 hover:bg-gray-50 dark:hover:bg-gray-800 transition"
        >
          <div class="flex items-center">
            <Checkbox
              state={userIds.includes(user.id) ? 'checked' : 'unchecked'}
              on:change={(e) => {
                if (e.detail === 'checked') {
                  userIds = [...userIds, user.id];
                } else {
                  userIds = userIds.filter((id) => id !== user.id);
                }
              }}
            />
          </div>

          <div class="flex w-full items-center justify-between">
            <Tooltip content={user.email} placement="top-start">
              <div class="flex items-center">
                <img
                  class="rounded-full w-10 h-10 object-cover mr-3"
                  src={user.profile_image_url.startsWith(WEBUI_BASE_URL) ||
                       user.profile_image_url.startsWith('https://www.gravatar.com/avatar/') ||
                       user.profile_image_url.startsWith('data:')
                       ? user.profile_image_url
                       : `${WEBUI_BASE_URL}/user.png`}
                  alt="user"
                />
                <div class="font-medium self-center text-gray-700 dark:text-gray-200">{user.name}</div>
              </div>
            </Tooltip>

            {#if userIds.includes(user.id)}
              <Badge type="success" content="member" />
            {/if}
          </div>
        </div>
      {/each}
    {:else}
      <div class="text-gray-500 text-xs text-center py-4 px-4 rounded-lg border border-gray-100 dark:border-gray-800 bg-white dark:bg-gray-900 shadow-sm">
        {$i18n.t('No users were found.')}
      </div>
    {/if}
  </div>
</div>

