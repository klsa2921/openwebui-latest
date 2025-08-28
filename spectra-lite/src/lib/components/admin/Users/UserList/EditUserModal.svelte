<script lang="ts">
	import { toast } from 'svelte-sonner';
	import dayjs from 'dayjs';
	import { createEventDispatcher } from 'svelte';
	import { onMount, getContext } from 'svelte';

	import { updateUserById } from '$lib/apis/users';

	import Modal from '$lib/components/common/Modal.svelte';
	import localizedFormat from 'dayjs/plugin/localizedFormat';
	import XMark from '$lib/components/icons/XMark.svelte';

	const i18n = getContext('i18n');
	const dispatch = createEventDispatcher();
	dayjs.extend(localizedFormat);

	export let show = false;
	export let selectedUser;
	export let sessionUser;

	let _user = {
		profile_image_url: '',
		role: 'pending',
		name: '',
		email: '',
		password: ''
	};

	const submitHandler = async () => {
		const res = await updateUserById(localStorage.token, selectedUser.id, _user).catch((error) => {
			toast.error(`${error}`);
		});

		if (res) {
			dispatch('save');
			show = false;
		}
	};

	onMount(() => {
		if (selectedUser) {
			_user = selectedUser;
			_user.password = '';
		}
	});
</script>

<Modal size="sm" bind:show>
	<div>
		<div class=" flex justify-between dark:text-gray-300 px-5 pt-4 pb-2">
			<div class=" text-lg font-medium self-center">{$i18n.t('Edit User')}</div>
			<button
				class="self-center"
				on:click={() => {
					show = false;
				}}
			>
				<XMark className={'size-5'} />
			</button>
		</div>

		<div class="flex flex-col md:flex-row w-full md:space-x-4 dark:text-gray-200">
			<div class="flex flex-col w-full sm:flex-row sm:justify-center sm:space-x-6">
	<form
		class="flex flex-col w-full bg-white dark:bg-gray-900 border border-gray-200 dark:border-gray-700 shadow-md rounded-xl overflow-hidden"
		on:submit|preventDefault={() => {
			submitHandler();
		}}
	>
		<!-- Profile header -->
		<div class="flex items-center border-b border-gray-200 dark:border-gray-700 px-5 py-3 bg-gray-50 dark:bg-gray-800">
			<div class="self-center mr-5">
				<img
					src={selectedUser.profile_image_url}
					class="w-14 h-14 object-cover rounded-full border border-gray-200 dark:border-gray-700 shadow-sm"
					alt="User profile"
				/>
			</div>

			<div>
				<div class="capitalize font-semibold text-gray-900 dark:text-gray-100">
					{selectedUser.name}
				</div>
				<div class="text-xs text-gray-500 dark:text-gray-400">
					{$i18n.t('Created at')} {dayjs(selectedUser.created_at * 1000).format('LL')}
				</div>
			</div>
		</div>

		<!-- Form body -->
		<div class="px-5 pt-4 pb-5 space-y-4">
			<!-- Role -->
			<div class="flex flex-col">
				<label class="mb-1 text-xs font-medium text-gray-500 dark:text-gray-400">
					{$i18n.t('Role')}
				</label>
				<select
					class="w-full text-sm px-3 py-2 rounded-lg border border-gray-200 dark:border-gray-700 
					bg-white dark:bg-gray-800 text-gray-900 dark:text-gray-100 
					focus:ring-2 focus:ring-blue-500 outline-none transition disabled:text-gray-500 dark:disabled:text-gray-500"
					bind:value={_user.role}
					disabled={_user.id == sessionUser.id}
					required
				>
					<option value="admin">{$i18n.t('Admin')}</option>
					<option value="user">{$i18n.t('User')}</option>
					<option value="pending">{$i18n.t('Pending')}</option>
				</select>
			</div>

			<!-- Email -->
			<div class="flex flex-col">
				<label class="mb-1 text-xs font-medium text-gray-500 dark:text-gray-400">
					{$i18n.t('Email')}
				</label>
				<input
					class="w-full text-sm px-3 py-2 rounded-lg border border-gray-200 dark:border-gray-700 
					bg-white dark:bg-gray-800 text-gray-900 dark:text-gray-100 
					focus:ring-2 focus:ring-blue-500 outline-none transition"
					type="email"
					bind:value={_user.email}
					placeholder={$i18n.t('Enter Your Email')}
					autocomplete="off"
					required
				/>
			</div>

			<!-- Name -->
			<div class="flex flex-col">
				<label class="mb-1 text-xs font-medium text-gray-500 dark:text-gray-400">
					{$i18n.t('Name')}
				</label>
				<input
					class="w-full text-sm px-3 py-2 rounded-lg border border-gray-200 dark:border-gray-700 
					bg-white dark:bg-gray-800 text-gray-900 dark:text-gray-100 
					focus:ring-2 focus:ring-blue-500 outline-none transition"
					type="text"
					bind:value={_user.name}
					placeholder={$i18n.t('Enter Your Name')}
					autocomplete="off"
					required
				/>
			</div>

			<!-- New Password -->
			<div class="flex flex-col">
				<label class="mb-1 text-xs font-medium text-gray-500 dark:text-gray-400">
					{$i18n.t('New Password')}
				</label>
				<input
					class="w-full text-sm px-3 py-2 rounded-lg border border-gray-200 dark:border-gray-700 
					bg-white dark:bg-gray-800 text-gray-900 dark:text-gray-100 
					focus:ring-2 focus:ring-blue-500 outline-none transition"
					type="password"
					placeholder={$i18n.t('Enter New Password')}
					bind:value={_user.password}
					autocomplete="new-password"
				/>
			</div>
		</div>

		<!-- Footer -->
		<div class="flex justify-end px-5 pb-4">
			<button
				class="px-4 py-2 text-sm font-medium rounded-full 
				bg-blue-600 hover:bg-blue-700 text-white 
				dark:bg-blue-500 dark:hover:bg-blue-600 
				transition flex flex-row items-center space-x-1 shadow-sm"
				type="submit"
			>
				{$i18n.t('Save')}
			</button>
		</div>
	</form>
</div>

		</div>
	</div>
</Modal>

<style>
	input::-webkit-outer-spin-button,
	input::-webkit-inner-spin-button {
		/* display: none; <- Crashes Chrome on hover */
		-webkit-appearance: none;
		margin: 0; /* <-- Apparently some margin are still there even though it's hidden */
	}

	.tabs::-webkit-scrollbar {
		display: none; /* for Chrome, Safari and Opera */
	}

	.tabs {
		-ms-overflow-style: none; /* IE and Edge */
		scrollbar-width: none; /* Firefox */
	}

	input[type='number'] {
		-moz-appearance: textfield; /* Firefox */
	}
</style>
