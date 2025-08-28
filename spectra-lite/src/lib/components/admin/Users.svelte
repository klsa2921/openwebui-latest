<script>
	import { getContext, tick, onMount } from 'svelte';
	import { toast } from 'svelte-sonner';

	import { goto } from '$app/navigation';
	import { user } from '$lib/stores';
	import { page } from '$app/stores';

	import UserList from './Users/UserList.svelte';
	import Groups from './Users/Groups.svelte';
	import { User, Users } from "lucide-svelte";

	const i18n = getContext('i18n');

	let selectedTab;
	$: {
		const pathParts = $page.url.pathname.split('/');
		const tabFromPath = pathParts[pathParts.length - 1];
		selectedTab = ['overview', 'groups'].includes(tabFromPath) ? tabFromPath : 'overview';
	}

	$: if (selectedTab) {
		// scroll to selectedTab
		scrollToTab(selectedTab);
	}

	const scrollToTab = (tabId) => {
		const tabElement = document.getElementById(tabId);
		if (tabElement) {
			tabElement.scrollIntoView({ behavior: 'smooth', block: 'nearest', inline: 'start' });
		}
	};

	let loaded = false;

	onMount(async () => {
		if ($user?.role !== 'admin') {
			await goto('/');
		}

		loaded = true;

		const containerElement = document.getElementById('users-tabs-container');

		if (containerElement) {
			containerElement.addEventListener('wheel', function (event) {
				if (event.deltaY !== 0) {
					// Adjust horizontal scroll position based on vertical scroll
					containerElement.scrollLeft += event.deltaY;
				}
			});
		}

		// Scroll to the selected tab on mount
		scrollToTab(selectedTab);
	});
</script>

<div class="flex flex-col lg:flex-row w-full h-full pb-2 lg:space-x-4">
	<div
	id="users-tabs-container"
	class="flex flex-row overflow-x-auto gap-2.5 max-w-full lg:gap-1 lg:flex-col lg:flex-none lg:w-44 text-sm font-medium text-left scrollbar-none"
>
	<!-- Overview Tab -->
	<button
		id="overview"
		class="flex items-center gap-2 px-3 py-2 min-w-fit rounded-lg border transition
			{selectedTab === 'overview'
				? 'bg-gray-100 dark:bg-gray-800 border-gray-300 dark:border-gray-700 text-black dark:text-white'
				: 'bg-transparent border-transparent text-gray-500 dark:text-gray-400 hover:bg-gray-50 dark:hover:bg-gray-900 hover:text-black dark:hover:text-white'}"
		on:click={() => {
			goto('/admin/users/overview');
		}}
	>
		<User class="size-4" />
		<span>{$i18n.t('Overview')}</span>
	</button>

	<!-- Groups Tab -->
	<button
		id="groups"
		class="flex items-center gap-2 px-3 py-2 min-w-fit rounded-lg border transition
			{selectedTab === 'groups'
				? 'bg-gray-100 dark:bg-gray-800 border-gray-300 dark:border-gray-700 text-black dark:text-white'
				: 'bg-transparent border-transparent text-gray-500 dark:text-gray-400 hover:bg-gray-50 dark:hover:bg-gray-900 hover:text-black dark:hover:text-white'}"
		on:click={() => {
			goto('/admin/users/groups');
		}}
	>
		<Users class="size-4" />
		<span>{$i18n.t('Groups')}</span>
	</button>
</div>

<div class="flex-1 mt-1 lg:mt-0 overflow-y-scroll">
	{#if selectedTab === 'overview'}
		<UserList />
	{:else if selectedTab === 'groups'}
		<Groups />
	{/if}
</div>
</div>
