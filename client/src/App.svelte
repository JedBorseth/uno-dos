<script lang="ts">
  import svelteLogo from "./assets/svelte.svg";
  import viteLogo from "/vite.svg";

  let chat = $state(["I dont know how to use svelte"]);
  if (window["WebSocket"]) {
    // using var because i can :)
    var conn = new WebSocket("ws://127.0.0.1:8080/ws");
    conn.onclose = function (evt) {
      chat = ["Connection closed."];
    };
    conn.onmessage = function (evt) {
      chat.push(evt.data);
    };
  } else {
    chat = ["Your browser does not support WebSockets."];
  }
  const sendMessage = (e: any) => {
    e.preventDefault();
    if (!conn || !e.target.message.value) {
      return;
    }
    conn.send(e.target.message.value);
    e.target.message.value = "";
  };
</script>

<main>
  <div>
    <a href="https://vite.dev" target="_blank" rel="noreferrer">
      <img src={viteLogo} class="logo" alt="Vite Logo" />
    </a>
    <a href="https://svelte.dev" target="_blank" rel="noreferrer">
      <img src={svelteLogo} class="logo svelte" alt="Svelte Logo" />
    </a>
  </div>
  <h1>Vite + Svelte</h1>
  <div class="card">
    {#each chat as item}
      <li>{item}</li>
    {/each}
  </div>
  <form onsubmit={sendMessage}>
    <input type="text" name="message" id="message" />
    <button type="submit">Send</button>
  </form>
</main>

<style>
  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00aa);
  }
</style>
