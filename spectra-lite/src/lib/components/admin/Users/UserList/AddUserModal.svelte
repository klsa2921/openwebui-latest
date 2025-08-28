<script lang="ts">
	import { toast } from 'svelte-sonner';
	import { createEventDispatcher } from 'svelte';
	import { onMount, getContext } from 'svelte';
	import { addUser } from '$lib/apis/auths';

	import { WEBUI_BASE_URL } from '$lib/constants';

	import Spinner from '$lib/components/common/Spinner.svelte';
	import Modal from '$lib/components/common/Modal.svelte';
	import { generateInitialsImage } from '$lib/utils';
	import XMark from '$lib/components/icons/XMark.svelte';

	const i18n = getContext('i18n');
	const dispatch = createEventDispatcher();

	export let show = false;

	let loading = false;
	let tab = '';
	let inputFiles;

	let _user = {
		name: '',
		email: '',
		password: '',
		role: 'user'
	};

	$: if (show) {
		_user = {
			name: '',
			email: '',
			password: '',
			role: 'user'
		};
	}

	const submitHandler = async () => {
		const stopLoading = () => {
			dispatch('save');
			loading = false;
		};

		if (tab === '') {
			loading = true;

			const res = await addUser(
				localStorage.token,
				_user.name,
				_user.email,
				_user.password,
				_user.role,
				generateInitialsImage(_user.name)
			).catch((error) => {
				toast.error(`${error}`);
			});

			if (res) {
				stopLoading();
				show = false;
			}
		} else {
			if (inputFiles) {
				loading = true;

				const file = inputFiles[0];
				const reader = new FileReader();

				reader.onload = async (e) => {
					const csv = e.target.result;
					const rows = csv.split('\n');

					let userCount = 0;

					for (const [idx, row] of rows.entries()) {
						const columns = row.split(',').map((col) => col.trim());
						console.debug(idx, columns);

						if (idx > 0) {
							if (
								columns.length === 4 &&
								['admin', 'user', 'pending'].includes(columns[3].toLowerCase())
							) {
								const res = await addUser(
									localStorage.token,
									columns[0],
									columns[1],
									columns[2],
									columns[3].toLowerCase(),
									generateInitialsImage(columns[0])
								).catch((error) => {
									toast.error(`Row ${idx + 1}: ${error}`);
									return null;
								});

								if (res) {
									userCount = userCount + 1;
								}
							} else {
								toast.error(`Row ${idx + 1}: invalid format.`);
							}
						}
					}

					toast.success(`Successfully imported ${userCount} users.`);
					inputFiles = null;
					const uploadInputElement = document.getElementById('upload-user-csv-input');

					if (uploadInputElement) {
						uploadInputElement.value = null;
					}

					stopLoading();
				};

				reader.readAsText(file, 'utf-8');
			} else {
				toast.error($i18n.t('File not found.'));
			}
		}

		loading = false;
	};
</script>

<Modal size="sm" bind:show>
	<div>
		<div class=" flex justify-between dark:text-gray-300 px-5 pt-4 pb-2">
			<div class=" text-lg font-medium self-center">{$i18n.t('Add User')}</div>
			<button
				class="self-center"
				on:click={() => {
					show = false;
				}}
			>
				<XMark className={'size-5'} />
			</button>
		</div>

		<div class="flex flex-col md:flex-row w-full px-4 pb-3 md:space-x-4 dark:text-gray-200">
			<div class="flex flex-col w-full sm:flex-row sm:justify-center sm:space-x-6">
	<form
		class="flex flex-col w-full max-w-lg bg-white dark:bg-gray-900 
			rounded-2xl shadow-lg border border-gray-200 dark:border-gray-800 
			p-6 transition"
		on:submit|preventDefault={() => submitHandler()}
	>
		<!-- Tabs -->
		<div
			class="flex mb-5 gap-2 overflow-x-auto scrollbar-none text-center text-sm font-medium"
		>
			<button
				class="px-4 py-2 rounded-full transition
					{tab === '' 
						? 'bg-blue-600 text-white dark:bg-blue-500 dark:text-white shadow-sm' 
						: 'text-gray-500 dark:text-gray-400 hover:text-gray-800 dark:hover:text-gray-200'}"
				type="button"
				on:click={() => (tab = '')}
			>
				{$i18n.t('Form')}
			</button>

			<!-- Uncomment if CSV Import needed -->
			<!--
			<button
				class="px-4 py-2 rounded-full transition
					{tab === 'import' 
						? 'bg-blue-600 text-white dark:bg-blue-500 dark:text-white shadow-sm' 
						: 'text-gray-500 dark:text-gray-400 hover:text-gray-800 dark:hover:text-gray-200'}"
				type="button"
				on:click={() => (tab = 'import')}
			>
				{$i18n.t('CSV Import')}
			</button>
			-->
		</div>

		<!-- Content -->
		<div>
			{#if tab === ''}
				<!-- Role -->
				<div class="flex flex-col w-full mb-4">
					<label class="mb-1 text-xs font-medium text-gray-600 dark:text-gray-300">
						{$i18n.t('Role')}
					</label>
					<select
						class="w-full capitalize rounded-lg text-sm px-3 py-2 
							border border-gray-300 dark:border-gray-700 
							bg-gray-50 dark:bg-gray-800 
							shadow-sm focus:ring-2 focus:ring-blue-400 dark:focus:ring-blue-600 
							transition outline-none"
						bind:value={_user.role}
						required
					>
						<option value="pending"> {$i18n.t('pending')} </option>
						<option value="user"> {$i18n.t('user')} </option>
						<option value="admin"> {$i18n.t('admin')} </option>
					</select>
				</div>

				<!-- Name -->
				<div class="flex flex-col w-full mb-4">
					<label class="mb-1 text-xs font-medium text-gray-600 dark:text-gray-300">
						{$i18n.t('Name')}
					</label>
					<input
						class="w-full text-sm px-3 py-2 rounded-lg border border-gray-300 dark:border-gray-700 
							bg-gray-50 dark:bg-gray-800 
							shadow-sm focus:ring-2 focus:ring-blue-400 dark:focus:ring-blue-600 
							transition outline-none"
						type="text"
						bind:value={_user.name}
						placeholder={$i18n.t('Enter Your Full Name')}
						required
						autocomplete="off"
					/>
				</div>

				<!-- Email -->
				<div class="flex flex-col w-full mb-4">
					<label class="mb-1 text-xs font-medium text-gray-600 dark:text-gray-300">
						{$i18n.t('Email')}
					</label>
					<input
						class="w-full text-sm px-3 py-2 rounded-lg border border-gray-300 dark:border-gray-700 
							bg-gray-50 dark:bg-gray-800 
							shadow-sm focus:ring-2 focus:ring-blue-400 dark:focus:ring-blue-600 
							transition outline-none"
						type="email"
						bind:value={_user.email}
						placeholder={$i18n.t('Enter Your Email')}
						required
					/>
				</div>

				<!-- Password -->
				<div class="flex flex-col w-full mb-4">
					<label class="mb-1 text-xs font-medium text-gray-600 dark:text-gray-300">
						{$i18n.t('Password')}
					</label>
					<input
						class="w-full text-sm px-3 py-2 rounded-lg border border-gray-300 dark:border-gray-700 
							bg-gray-50 dark:bg-gray-800 
							shadow-sm focus:ring-2 focus:ring-blue-400 dark:focus:ring-blue-600 
							transition outline-none"
						type="password"
						bind:value={_user.password}
						placeholder={$i18n.t('Enter Your Password')}
						autocomplete="off"
					/>
				</div>
			{:else if tab === 'import'}
				<!-- Import CSV -->
				<div>
					<div class="mb-3 w-full">
						<input
							id="upload-user-csv-input"
							hidden
							bind:files={inputFiles}
							type="file"
							accept=".csv"
						/>

						<button
							class="w-full text-sm font-medium py-3 bg-gray-50 dark:bg-gray-800 
								border-2 border-dashed border-gray-300 dark:border-gray-700 
								rounded-xl hover:bg-gray-100 dark:hover:bg-gray-700 
								shadow-sm transition"
							type="button"
							on:click={() => document.getElementById('upload-user-csv-input')?.click()}
						>
							{#if inputFiles}
								{inputFiles.length > 0 ? `${inputFiles.length}` : ''} document(s) selected.
							{:else}
								{$i18n.t('Click here to select a csv file.')}
							{/if}
						</button>
					</div>

					<div class="text-xs text-gray-500 dark:text-gray-400">
						ⓘ {$i18n.t(
							'Ensure your CSV file includes 4 columns in this order: Name, Email, Password, Role.'
						)}
						<a
							class="underline dark:text-gray-200 hover:text-blue-500 dark:hover:text-blue-400 transition"
							href="{WEBUI_BASE_URL}/static/user-import.csv"
						>
							{$i18n.t('Click here to download user import template file.')}
						</a>
					</div>
				</div>
			{/if}
		</div>

		<!-- Save button -->
		<div class="flex justify-end pt-5">
			<button
				class="px-5 py-2 text-sm font-medium rounded-lg shadow-md
					bg-blue-600 text-white hover:bg-blue-700 
					dark:bg-blue-500 dark:hover:bg-blue-600
					transition flex items-center space-x-2
					{loading ? ' cursor-not-allowed opacity-70' : ''}"
				type="submit"
				disabled={loading}
			>
				<span>{$i18n.t('Save')}</span>

				{#if loading}
					<div class="self-center">
						<Spinner />
					</div>
				{/if}
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
