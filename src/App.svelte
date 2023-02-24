<script lang="ts">
  import Counter from './lib/Counter.svelte'
  import {
    createConnectTransport,
    createPromiseClient,
  } from "@bufbuild/connect-web";

  // Import service definition that you want to connect to.
  import { ElizaService } from "@buf/bufbuild_eliza.bufbuild_connect-es/buf/connect/demo/eliza/v1/eliza_connect";

  // The transport defines what type of endpoint we're hitting.
  // In our example we'll be communicating with a Connect endpoint.
  // If your endpoint only supports gRPC-web, make sure to use
  // `createGrpcWebTransport` instead.
  const transport = createConnectTransport({
    baseUrl: "https://demo.connect.build",
  });

// Here we make the client itself, combining the service
// definition with the transport.
const client = createPromiseClient(ElizaService, transport);


  let message = "";
  let messageHistory = ["hello", "how can i help"];

  const send = async () => {
    messageHistory = [...messageHistory, message];
    const response = await client.say({
        sentence: message,
      });
    messageHistory = [...messageHistory, response.sentence];
    message = "";
  };
</script>

<main>
  <div class="eliza">
    <h1>Eliza</h1>
    <form on:submit|preventDefault={send}>
      <input type="text" bind:value={message} />
    </form>

    <ul class="history">
      {#each messageHistory as message}
        <li>{message}</li>
      {:else}
        <i>Time to start a conversation.</i>
      {/each}
    </ul>
  </div>

  <div class="card">
    <p>A simple test counter.</p>
    <Counter />
  </div>
</main>

<style>
  .eliza {
    text-align: left;
    min-width: 400px;
  }
  .eliza input {
    width: 100%;
  }
  .history {
    padding: 0px;
    list-style-type: none;
    max-height: 400;
    overflow: scroll;
  }
</style>
