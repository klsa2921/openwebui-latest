<script>
	import { toast } from 'svelte-sonner';
	import { getContext } from 'svelte';

	const i18n = getContext('i18n');

	import { deleteGroupById, updateGroupById } from '$lib/apis/groups';

	import Pencil from '$lib/components/icons/Pencil.svelte';
	import User from '$lib/components/icons/User.svelte';
	import UserCircleSolid from '$lib/components/icons/UserCircleSolid.svelte';
	import GroupModal from './EditGroupModal.svelte';

	export let users = [];
	export let group = {
		name: 'Admins',
		user_ids: [1, 2, 3]
	};

	export let setGroups = () => {};

	let showEdit = false;

	const updateHandler = async (_group) => {
		const res = await updateGroupById(localStorage.token, group.id, _group).catch((error) => {
			toast.error(`${error}`);
			return null;
		});

		if (res) {
			toast.success($i18n.t('Group updated successfully'));
			setGroups();
		}
	};

	const deleteHandler = async () => {
		const res = await deleteGroupById(localStorage.token, group.id).catch((error) => {
			toast.error(`${error}`);
			return null;
		});

		if (res) {
			toast.success($i18n.t('Group deleted successfully'));
			setGroups();
		}
	};
</script>

<GroupModal
	bind:show={showEdit}
	edit
	{users}
	{group}
	onSubmit={updateHandler}
	onDelete={deleteHandler}
/>

<button
	class="flex items-center justify-between w-full text-sm px-3 py-2 
	       border-b border-gray-100 dark:border-gray-800 
	       bg-white dark:bg-gray-900 hover:bg-gray-50 dark:hover:bg-gray-850 
	       transition rounded-lg last:border-b-0"
	on:click={() => { showEdit = true; }}
>
	<!-- Group name -->
	<div class="flex items-center gap-2 basis-3/5 text-left font-medium">
		<UserCircleSolid className="size-4 text-gray-500 dark:text-gray-400" />
		<span class="truncate">{group.name}</span>
	</div>

	<!-- User count + edit -->
	<div class="flex items-center justify-end gap-2 basis-2/5 text-right font-medium">
		<span>{group.user_ids.length}</span>
		<User className="size-3.5 text-gray-500 dark:text-gray-400" />

		<div class="rounded-md p-1 hover:bg-gray-100 dark:hover:bg-gray-800 transition">
			<Pencil className="size-3.5 text-gray-600 dark:text-gray-300" />
		</div>
	</div>
</button>
