<script>
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';

  let cardId = '';
  let name = '';
  let weight = '';

  onMount(() => {
    const unsubscribe = page.subscribe(p => {
      cardId = p.params.cardId;
    });
    return () => unsubscribe();
  });

  const submitForm = async () => {
    const res = await fetch('https://kampus-jwt3.onrender.com/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ cardId, name, weight })
    });

    const data = await res.json();
    if (res.ok) {
      alert('✅ User created! Please scan your card again.');
      goto('/');
    } else {
      alert(`❌ Error: ${data.message}`);
    }
  };
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

  form {
    display: grid;
    gap: 1rem;
  }

  label {
    font-weight: bold;
    display: block;
  }

  input {
    padding: 0.5rem;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 6px;
    width: 100%;
  }

  button {
    background-color: #6300ff;
    color: white;
    padding: 0.6rem 1rem;
    border: none;
    border-radius: 6px;
    font-weight: 600;
    cursor: pointer;
    transition: background 0.3s ease;
  }

  button:hover {
    background-color: #4e00cc;
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
  <h1>Register User</h1>
  <p>Complete the form below to create a new user</p>
</header>

<div class="container">
  <form on:submit|preventDefault={submitForm}>
    <div>
      <label for="name">Name</label>
      <input type="text" id="name" bind:value={name} required />
    </div>

    <div>
      <label for="weight">Weight (kg)</label>
      <input type="number" id="weight" bind:value={weight} required />
    </div>

    <button type="submit">Create User</button>
  </form>
</div>

<footer>
  <p>&copy; 2025 Health & Fitness Monitoring. All rights reserved.</p>
</footer>
