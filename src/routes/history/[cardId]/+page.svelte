<script>
    import { onMount } from 'svelte';
    import { page } from '$app/stores';
  
    let cardId = '';
    let sessions = [];
    let status = 'Loading session history...';
  
    onMount(async () => {
      const unsubscribe = page.subscribe(p => {
        cardId = p.params.cardId;
      });
  
      try {
        const res = await fetch(`https://kampus-jwt3.onrender.com/sessions/${cardId}`);
        const data = await res.json();
  
        if (res.ok) {
          sessions = data.sessions;
          status = sessions.length > 0 ? '' : 'No session history found.';
        } else {
          status = data.message || 'Error loading session history.';
        }
      } catch (err) {
        console.error('Error fetching history:', err);
        status = 'Server error while fetching session history.';
      }
  
      return () => unsubscribe();
    });
  
    function formatDate(iso) {
      return new Date(iso).toLocaleString();
    }
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
      background-color: #6300ff;
      color: white;
      text-align: center;
      padding: 1rem 0;
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
    }
  
    .session {
      border-bottom: 1px solid #eee;
      padding: 1rem 0;
    }
  
    .session:last-child {
      border: none;
    }
  
    .session h3 {
      margin: 0 0 0.5rem;
      color: #6300ff;
      font-size: 1.1rem;
    }
  
    .session p {
      margin: 0.2rem 0;
      font-size: 0.95rem;
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
    <h1>Session History</h1>
    <p>For Card ID: {cardId}</p>
  </header>

  <div style="margin-top: 2rem; text-align: center;">
    <a href={`/`} style="color: #6200ea; font-weight: bold; text-decoration: none;">
      📄 Click here to go back to main page.
    </a>
  </div>
  
  <div class="container">
    {#if status}
      <p>{status}</p>
    {:else}
      {#each sessions as s, i}
        <div class="session">
          <h3>Session {sessions.length - i}</h3>
          <p><strong>Start:</strong> {formatDate(s.startTime)}</p>
          <p><strong>End:</strong> {formatDate(s.endTime)}</p>
          <p><strong>Ticks:</strong> {s.tickCount}</p>
          <p><strong>Distance:</strong> {s.distance} meters</p>
          <p><strong>Calories:</strong> {s.calories} kcal</p>
        </div>
      {/each}
    {/if}
  </div>
  
  <footer>
    <p>&copy; 2025 Health & Fitness Monitoring. All rights reserved.</p>
  </footer>
  