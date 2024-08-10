<script>
  export let step;

  let selectedStep;

  $: {
    console.log(step);
    selectedStep = step;
  }

  function updateWorkflow() {
    console.log("hi: " + selectedStep.step_id);
  }
</script>

<div>
  <h4>{selectedStep.step_id}</h4>
  <div>
    <!-- <select bind:value={step.action} name="cars" id="cars">
      {#each ["button_selection", "send_message", "open_model"] as option}
        <option value={option}>{option}</option>
      {/each}
    </select> -->

    <textarea bind:value={selectedStep.message} type="text" />

    <!-- <ul>
      {#each step.branches as branch}
        <li>{branch.text} -> {branch.next_step_id}</li>
      {/each}
    </ul> -->

    <button on:click={updateWorkflow}>Save</button>
  </div>
</div>

<style>
  textarea {
    width: 90%;
  }
</style>
