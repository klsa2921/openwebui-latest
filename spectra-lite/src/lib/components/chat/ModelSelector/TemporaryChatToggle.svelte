<script>
  import { temporaryChatEnabled } from '$lib/stores';
  import { goto } from '$app/navigation';
    import ChatBubbleOval from '$lib/components/icons/ChatBubbleOval.svelte';

  async function handleClick() {
    temporaryChatEnabled.set(!$temporaryChatEnabled);
    await goto('/');
    const newChatButton = document.getElementById('new-chat-button');
    setTimeout(() => newChatButton?.click(), 0);

    // update URL
    if ($temporaryChatEnabled) {
      history.replaceState(null, '', '?temporary-chat=true');
    } else {
      history.replaceState(null, '', location.pathname);
    }
  }
</script>

<div class="flex items-center gap-3 mt-2">
  <!-- label -->
  <span class="text-sm text-gray-700 dark:text-gray-100">Temporary Chat</span>

  <!-- switch -->
  <div
    class="w-9 h-5 rounded-full relative cursor-pointer transition-colors"
    on:click={() => temporaryChatEnabled.set(!$temporaryChatEnabled)}
    style="background-color: {$temporaryChatEnabled ? '#3B82F6' : '#D1D5DB'}" 
  >
    <!-- knob -->
    <div
      class="absolute top-0.5 w-4 h-4 bg-white rounded-full shadow transition-all"
      style="left: {$temporaryChatEnabled ? '1rem' : '0.125rem'}"
    ></div>
  </div>
</div>
