<script lang="ts">
	import { toast } from 'svelte-sonner';
	import { getContext, onMount } from 'svelte';
	const i18n = getContext('i18n');

	import Spinner from '$lib/components/common/Spinner.svelte';
	import Modal from '$lib/components/common/Modal.svelte';
	import Display from './Display.svelte';
	import Permissions from './Permissions.svelte';
	import Users from './Users.svelte';
	import UserPlusSolid from '$lib/components/icons/UserPlusSolid.svelte';
	import WrenchSolid from '$lib/components/icons/WrenchSolid.svelte';
	import ConfirmDialog from '$lib/components/common/ConfirmDialog.svelte';
	import XMark from '$lib/components/icons/XMark.svelte';
    import { X, Settings, Lock, UserPlus } from 'lucide-svelte';

	export let onSubmit: Function = () => {};
	export let onDelete: Function = () => {};

	export let show = false;
	export let edit = false;

	export let users = [];
	export let group = null;

	export let custom = true;

	export let tabs = ['general', 'permissions', 'users'];

	let selectedTab = 'general';
	let loading = false;
	let showDeleteConfirmDialog = false;

	export let name = '';
	export let description = '';

	export let permissions = {
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
			temporary: true
		},
		features: {
			direct_tool_servers: false,
			web_search: true,
			image_generation: true,
			code_interpreter: true
		}
	};
	export let userIds = [];

	const submitHandler = async () => {
		loading = true;

		const group = {
			name,
			description,
			permissions,
			user_ids: userIds
		};

		await onSubmit(group);

		loading = false;
		show = false;
	};

	const init = () => {
		if (group) {
			name = group.name;
			description = group.description;
			permissions = group?.permissions ?? {};

			userIds = group?.user_ids ?? [];
		}
	};

	$: if (show) {
		init();
	}

	onMount(() => {
		selectedTab = tabs[0];
		init();
	});
</script>

<ConfirmDialog
	bind:show={showDeleteConfirmDialog}
	on:confirm={() => {
		onDelete();
		show = false;
	}}
/>

<Modal size="md" bind:show>
	<div class="bg-white dark:bg-gray-900 rounded-2xl shadow-xl overflow-hidden">
		<!-- Header -->
		<div class="flex justify-between items-center px-6 py-4 border-b border-gray-200 dark:border-gray-800">
			<div class="text-lg font-semibold text-gray-900 dark:text-gray-100">
				{#if custom}
					{#if edit}
						{$i18n.t('Edit User Group')}
					{:else}
						{$i18n.t('Add User Group')}
					{/if}
				{:else}
					{$i18n.t('Edit Default Permissions')}
				{/if}
			</div>
			<button
				class="p-1 rounded-full hover:bg-gray-100 dark:hover:bg-gray-800 transition"
				on:click={() => (show = false)}
			>
				<X class="w-5 h-5 text-gray-600 dark:text-gray-300" />
			</button>
		</div>

		<!-- Body -->
		<div class="flex flex-col md:flex-row w-full px-6 py-5 gap-4">
			<form
				class="flex flex-col w-full"
				on:submit={(e) => {
					e.preventDefault();
					submitHandler();
				}}
			>
				<div class="flex flex-col lg:flex-row w-full gap-4">
					<!-- Tabs -->
					<div
						id="admin-settings-tabs-container"
						class="flex flex-row lg:flex-col gap-2 text-sm font-medium"
					>
						{#if tabs.includes('general')}
							<button
								class="flex items-center px-3 py-2 rounded-xl transition 
									{selectedTab === 'general'
										? 'bg-black text-white dark:bg-white dark:text-black'
										: 'text-gray-500 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-800'}"
								type="button"
								on:click={() => (selectedTab = 'general')}
							>
								<Settings class="w-4 h-4 mr-2" />
								{$i18n.t('General')}
							</button>
						{/if}

						{#if tabs.includes('permissions')}
							<button
								class="flex items-center px-3 py-2 rounded-xl transition 
									{selectedTab === 'permissions'
										? 'bg-black text-white dark:bg-white dark:text-black'
										: 'text-gray-500 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-800'}"
								type="button"
								on:click={() => (selectedTab = 'permissions')}
							>
								<!-- svelte-ignore missing-declaration -->
								<Lock class="w-4 h-4 mr-2" />
								{$i18n.t('Permissions')}
							</button>
						{/if}

						{#if tabs.includes('users')}
							<button
								class="flex items-center px-3 py-2 rounded-xl transition 
									{selectedTab === 'users'
										? 'bg-black text-white dark:bg-white dark:text-black'
										: 'text-gray-500 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-800'}"
								type="button"
								on:click={() => (selectedTab = 'users')}
							>
								<UserPlus class="w-4 h-4 mr-2" />
								{$i18n.t('Users')} ({userIds.length})
							</button>
						{/if}
					</div>

					<!-- Tab Content -->
					<div class="flex-1 max-h-[22rem] overflow-y-auto scrollbar-hidden">
						{#if selectedTab == 'general'}
							<Display bind:name bind:description />
						{:else if selectedTab == 'permissions'}
							<Permissions bind:permissions />
						{:else if selectedTab == 'users'}
							<Users bind:userIds {users} />
						{/if}
					</div>
				</div>

				<!-- Footer Buttons -->
				<div class="flex justify-between items-center pt-5 gap-2">
					{#if edit}
						<button
							type="button"
							class="px-4 py-2 rounded-full text-sm font-medium border border-gray-300 dark:border-gray-700 text-gray-700 dark:text-gray-300 hover:bg-gray-100 dark:hover:bg-gray-800 transition"
							on:click={() => (showDeleteConfirmDialog = true)}
						>
							{$i18n.t('Delete')}
						</button>
					{:else}
						<div></div>
					{/if}

					<button
						type="submit"
						disabled={loading}
						class="px-4 py-2 rounded-full text-sm font-medium bg-black text-white hover:bg-gray-900 dark:bg-white dark:text-black dark:hover:bg-gray-100 transition flex items-center"
					>
						{$i18n.t('Save')}
						{#if loading}
							<Spinner class="ml-2 w-4 h-4" />
						{/if}
					</button>
				</div>
			</form>
		</div>
	</div>
</Modal>
