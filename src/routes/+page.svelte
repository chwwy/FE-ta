<script>
    import { goto } from '$app/navigation';
    import { onMount } from 'svelte';
  
    let status = 'Waiting for card...';
    let polling = false;
    let poller;
  
    onMount(() => {
      if (polling) return;
      polling = true;
      status = 'Listening for active session...';
  
      poller = setInterval(async () => {
        try {
          const res = await fetch('https://kampus-jwt3.onrender.com/sessions/active-latest');
          if (res.ok) {
            const data = await res.json();
            clearInterval(poller);
            polling = false;
            status = 'Session found! Redirecting...';
            goto(data.userExists ? `/sessions/${data.cardId}` : `/signup/${data.cardId}`);
          }
        } catch (err) {
          console.error('Polling error:', err);
        }
      }, 2000);
    });
  </script>
  
  <main class="p-6 max-w-xl mx-auto text-center">
    <h1 class="text-2xl font-bold mb-4">Tap your card to start</h1>
    <p class="text-gray-600">{status}</p>
  </main>
  