<script>
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();

  export let wf_id;

  let message;
  let action;

  async function createNewWorflowStep() {
    const body = {
      wf_id,
      message,
      action,
    };

    const response = await fetch(`http://localhost:5000/wf/step/create`, {
      method: "POST",
      body: JSON.stringify(body),
      headers: {
        "Content-Type": "application/json",
      },
      credentials: "include",
    });
    const data = await response.json();
    console.log(data);
    dispatch("notify", "default");
  }
</script>

<div>
  <select name="" id="" bind:value={action}>
    <option value="send_message">send_message</option>
    <option value="button_selection">button_selection</option>
    <!-- <option value="model">model</option> -->
  </select>
  <input type="text" placeholder="message" bind:value={message} />
  <button on:click={createNewWorflowStep}>New step</button>
</div>
