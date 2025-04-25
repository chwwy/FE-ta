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
    background-color: #6300ff; /* brighter purple */
    color: white;
    text-align: center;
    padding: 1rem 1rem;
  }

  header h1 {
    margin: 0;
    font-size: 2rem;
    font-weight: 700;
  }

  header p {
    margin: 1rem 0 0;
    font-size: 1rem;
    font-weight: 400;
  }

  .container {
    max-width: 800px;
    margin: 2rem auto;
    background: white;
    padding: 2rem;
    border-radius: 10px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    text-align: center;
  }

  .status {
    font-size: 1.2rem;
    color: #555;
    margin-top: 1rem;
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
  <h1>Health & Fitness Monitoring</h1>
  <p>Your all-in-one platform for health and fitness tracking</p>
</header>

<div class="container">
  <h2 class="text-xl font-bold mb-2">Waiting for card…</h2>
  <p class="status">{status}</p>
</div>

<footer>
  <p>&copy; 2025 Health & Fitness Monitoring. All rights reserved.</p>
</footer>
