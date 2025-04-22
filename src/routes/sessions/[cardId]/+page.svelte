<script>
  import { onMount } from 'svelte';
  import { page } from '$app/stores';

  let cardId   = $page.params.cardId;
  let session  = null;
  let status   = 'Waiting for session to finish…';

  const delay = ms => new Promise(r => setTimeout(r, ms));

  async function waitForSessionEndAndFetch() {
    while (true) {
      try {
        const active = await fetch('https://kampus-jwt3.onrender.com/sessions/active-latest');
        if (active.status === 404) break;
      } catch {
        break;
      }
      status = 'Session still running… Tap the card again to stop session.';
      await delay(2000);
    }

    status = 'Fetching final session data…';
    const res = await fetch(`https://kampus-jwt3.onrender.com/sessions/${cardId}`);
    if (!res.ok) {
      status = `Error fetching session: ${res.status}`;
      return;
    }

    const payload = await res.json();
    session = payload.sessions[0] ?? null;
    status  = null;
  }

  onMount(waitForSessionEndAndFetch);
</script>

<main class="p-6 max-w-xl mx-auto text-center">
  <h2 class="text-xl font-bold mb-4">Treadmill Session</h2>

  {#if status}
    <p class="text-gray-600">{status}</p>
  {:else if session}
    <div class="mt-4 text-left space-y-1">
      <p><strong>Card:</strong> {cardId}</p>
      <p><strong>Ticks:</strong>   {session.tickCount}</p>
      <p><strong>Distance:</strong> {session.distance} m</p>
      <p><strong>Calories:</strong> {session.calories} kcal</p>
    </div>
  {/if}
</main>
