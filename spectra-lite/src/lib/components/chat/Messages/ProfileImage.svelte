<script lang="ts">
	import { WEBUI_BASE_URL } from '$lib/constants';

	export let className = 'size-8';
	export let src = `${WEBUI_BASE_URL}/static/favicon.png`;
</script>

<img
	crossorigin="anonymous"
	src={src === '' || src === null || src === undefined
		? `${WEBUI_BASE_URL}/static/favicon.png`
		: src.startsWith(WEBUI_BASE_URL) ||
			  src.startsWith('https://www.gravatar.com/avatar/') ||
			  src.startsWith('https://') ||
			  src.startsWith('http://') ||
			  src.startsWith('data:') ||
			  src.startsWith('/')
			? src
			: `${WEBUI_BASE_URL}/user.png`}
	class=" {className} object-cover rounded-full"
	alt="profile"
	draggable="false"
	on:error={(e) => {
		console.log('Profile image failed to load:', e.target.src);
		e.target.src = `${WEBUI_BASE_URL}/user.png`;
	}}
/>
