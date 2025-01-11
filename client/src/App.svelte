<script lang="ts">
  import Card from "./components/Card.svelte";

  let chat = $state<string[]>([]);
  let conn: WebSocket;
  if (window["WebSocket"]) {
    conn = new WebSocket("ws://127.0.0.1:8080/ws");
    conn.onclose = () => {
      console.log("Connection closed.");
    };
    conn.onmessage = (evt) => {
      chat.push(evt.data);
    };
  } else {
    throw new Error("WebSocket is not supported by your browser.");
  }

  const handleSubmit = (
    e: SubmitEvent & {
      currentTarget: EventTarget & HTMLFormElement;
    }
  ) => {
    e.preventDefault();
    const formData = new FormData(e.target as HTMLFormElement);
    const message = formData.get("message");
    if (!conn || !message) {
      return;
    }
    conn.send(message);
    e.currentTarget.reset();
  };
</script>

<main class="flex flex-col items-center justify-center h-screen gap-3">
  <h1 class="text-4xl">Uno by Joshua And Jedsen</h1>
  <div>
    {#each chat as item}
      <li>{item}</li>
    {/each}
  </div>
  <form onsubmit={handleSubmit}>
    <input
      class="border px-2 py-1"
      type="text"
      name="message"
      id="message"
      placeholder="ex. a1b2c3"
    />
    <button
      class="px-2 py-1 text-sm bg-blue-600 rounded-lg text-white hover:bg-blue-600/70"
      type="submit">Join Lobby</button
    >
  </form>
  <h2>Or</h2>
  <a
    href="/game"
    class="px-2 py-1 text-sm bg-blue-600 rounded-lg text-white hover:bg-blue-600/70"
    >Create Lobby</a
  >
  <div class="flex items-center absolute top-0">
    <Card
      card={{ color: "green", numberValue: 2, type: "number" }}
      disableMouseEvents
    />
    <h2 class="text-4xl">.</h2>
    <Card
      card={{ color: "red", numberValue: 0, type: "number" }}
      disableMouseEvents
    />
  </div>
</main>
