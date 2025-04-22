<script>
    import { page } from '$app/stores';
    import { goto } from '$app/navigation';
    let cardId = $page.params.cardId;
    let name = '';
    let weight = '';
    let message = '';
  
    async function handleSignup() {
      const res = await fetch('/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ cardId, name, weight })
      });
      const data = await res.json();
      if (res.ok) {
        goto(`/session/${cardId}`);
      } else {
        message = data.message || 'Signup failed';
      }
    }
  </script>
  
  <main class="p-6 max-w-xl mx-auto text-center">
    <h2 class="text-xl font-bold mb-2">Sign Up</h2>
    <p class="mb-2">Card ID: {cardId}</p>
    <input bind:value={name} placeholder="Name" class="border p-2 rounded w-full mb-2" />
    <input bind:value={weight} placeholder="Weight" class="border p-2 rounded w-full mb-2" />
    <button class="bg-green-500 text-white px-4 py-2 rounded" on:click={handleSignup}>Submit</button>
    {#if message}<p class="text-red-500 mt-2">{message}</p>{/if}
  </main>