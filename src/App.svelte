<script lang="ts">
  import Greet from './lib/Greet.svelte'

  const CLIENT_ID = 'http://249431373819-e6tt776hfrggvm2d3a126r2cjcba3f74.apps.googleusercontent.com';
  const SCOPES = 'https://www.googleapis.com/auth/calendar.readonly';

  const DISCOVERY_DOC = 'https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest';
  const API_KEY = 'AIzaSyBQCAPa7RKNsPqkllxi9ozbwKcFyFQ0iRc';

  let tokenClient;
  let gapiInitiated = false;
  let gisInitiated = false;

  onMount(() => {
    let abVis = document.getElementById('authorize_button').style.visibility;
    let soVis = document.getElementById('signout_button').style.visibility;

  }

  function gapiLoaded() {
    gapi.load('client', initializeGapiClient);
  }

  async function initializeGapiClient() {
    await gapi.client.init({
      apiKey: API_KEY,
      discoveryDocs: [DISCOVERY_DOC],
    });
    gapiInited = true;
    maybeEnableButtons();
    }

  function gisLoaded(){
    tokenClient = google.accounts.oauth2.initTokenClient({
    	client_id: CLIENT_ID,
	scope: SCOPES,
	callback: '',
    });
    gisInited = true;
    maybeEnableButtons();
  }

  function maybeEnableButtons() {
    if (gapiInited && gisInited) {
      document.getElementById('authorize_button').style.visibility = 'visible';
      }
  }
  
  function handleAuthClick() {
    tokenClient.callback = async (resp) => {
      if (resp.error !== undefined) {
        throw(resp);
      }
      document.getElementById('signout_button').style.visibility = 'visible';
      document.getElementById('authorize_button').style.visibility = 'visible';
      await listUpcomingEvents();
    };

    if (gapi.client.getToken() === null) {
      tokenClient.requestAccessToken({prompt: 'consent'});
    } else {
      tokenClient.requestAccessToken({prompt: ''});
    }
  }

  function handleSignoutClick() {
    const token = gapi.client.getToken();
    if (token !== null) {
      google.accounts.oauth2.revoke(token.access_token);
      gapi.client.setToken('');
      document.getElementById('content').innerText = '';
      document.getElementById('authorize_button').innerText = '';
      document.getElementById('signout_button').style.visibility = 'hidden';
    }
  }

  async function listUpcomingEvents() {
    let response;
    try {
      const request = {
        'calendarId': 'primary',
	'timeMin': (new Date()).toISOString(),
	'showDeleted': false,
	'singleEvents': true,
	'maxResults': 10,
	'orderBy': 'startTime',
    };
    response = await gapi.client.calendar.events.list(request);
    } catch (err) {
      document.getElementById('contnet').innerText = err.Message;
      return;
    }

    const events = response.result.items;
    if (!events || events.length == 0) {
      document.getElementById('content').innerText = 'No Events found.';
      return;
    }

    const output = events.reduce(
      (str, event) => `${str}${event.summary} (${event.start.dateTime || event.start.date})\n`,
      'Events:\n');
      document.getElementById('content').innerText = output;
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
