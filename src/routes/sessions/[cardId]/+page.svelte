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
    status = session ? null : 'No session found.';
  }

  onMount(waitForSessionEndAndFetch);
</script>

<svelte:head>
  <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;700&display=swap" rel="stylesheet" />
</svelte:head>

<style>
  body {
    font-family: 'Roboto', sans-serif;
    background-color: #f4f4f9;
    margin: 0;
  }

  header {
    background-color: #6200ea;
    color: white;
    text-align: center;
    padding: 1rem 2rem;
  }

  .container {
    max-width: 800px;
    margin: 2rem auto;
    background: white;
    padding: 2rem;
    border-radius: 10px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  }

  .stat {
    margin: 1rem 0;
    font-size: 1.2rem;
  }

  footer {
    background: #333;
    color: white;
    text-align: center;
    padding: 1rem 0;
    margin-top: 2rem;
  }
</style>

<header>
  <h1>Session Results</h1>
  <p>Card: {cardId}</p>
</header>

<div class="container">
  {#if status}
    <p class="stat">{status}</p>
  {:else if session}
    <div class="stat"><strong>Distance:</strong> {session.distance} meters</div>
    <div class="stat"><strong>Calories:</strong> {session.calories} kcal</div>

    <div style="margin-top: 2rem; text-align: center;">
      <a href={`/history/${cardId}`} style="color: #6200ea; font-weight: bold; text-decoration: none;">
        📄 Click here to see your walking sessions
      </a>
    </div>
  {/if}
</div>


<footer>
  <p>&copy; 2025 Health & Fitness Monitoring. All rights reserved.</p>
</footer>
