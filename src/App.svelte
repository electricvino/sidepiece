<script lang="ts">
  import Greet from './lib/Greet.svelte'

  const CLIENT_ID = '';
  const API_KEY = '';

  const DISCOVERY_DOC = 'https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest';
  const SCOPES = '';

  let tokenClient;
  let gapInitiated = false;
  let gisInitiated = false;

  //document.getElementById('authorize_button').style.visibility = 'hidden';
  //document.getElementById('signout_button').style.visibility = 'hidden';

  function gapiLoaded() 
  function handleAuthClick() {
    
  }

  function handleSignoutClick() {
    
  }
</script>

<main class="container">
  <h1>Let's make some epic shit! </h1>

  <div class="row">
    <a href="https://vitejs.dev" target="_blank">
      <img src="/vite.svg" class="logo vite" alt="Vite Logo" />
    </a>
    <a href="https://tauri.app" target="_blank">
      <img src="/tauri.svg" class="logo tauri" alt="Tauri Logo" />
    </a>
    <a href="https://svelte.dev" target="_blank">
      <img src="/svelte.svg" class="logo svelte" alt="Svelte Logo" />
    </a>
  </div>

  <p>
    Click on the Tauri, Vite, and Svelte logos to learn more.
  </p>

  <div class="row">
    <Greet />
  </div>

  <button id="authorize_button" on:click|once={handleAuthClick}>Authorize</button>
  <button id="signout_button" on:click={handleSignoutClick}>Sign Out</button>

</main>

<style>
  .logo.vite:hover {
    filter: drop-shadow(0 0 2em #747bff);
  }

  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00);
  }
</style>
