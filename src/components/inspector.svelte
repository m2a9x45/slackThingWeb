<script>
  import { createEventDispatcher } from "svelte";

  export let step;
  export let stepIDs;

  const dispatch = createEventDispatcher();
  let selectedStep;

  $: {
    console.log(step);
    selectedStep = step;
  }

  async function updateWorkflow() {
    console.log("hi: " + selectedStep.step_id);

    const response = await fetch(`http://localhost:5000/wf/step/update`, {
      method: "POST",
      body: JSON.stringify(selectedStep),
      headers: {
        "Content-Type": "application/json",
      },
      credentials: "include",
    });
    // const data = await response.json();
    // console.log(data);

    dispatch("notify", "default");
  }

  let nextStepID;
  let action;

  async function createWorkflowBranch() {
    const body = {
      step_id: selectedStep.step_id,
      next_step_id: nextStepID,
      action,
    };

    const response = await fetch(
      `http://localhost:5000/wf/step/branch/create`,
      {
        method: "POST",
        body: JSON.stringify(body),
        headers: {
          "Content-Type": "application/json",
        },
        credentials: "include",
      }
    );
    // const data = await response.json();
    // console.log(data);

    dispatch("notify", selectedStep.step_id);
  }
</script>

<div>
  <h3>StepID: {selectedStep.step_id}</h3>
  <div>
    <!-- <select bind:value={step.action} name="cars" id="cars">
      {#each ["button_selection", "send_message", "open_model"] as option}
        <option value={option}>{option}</option>
      {/each}
    </select> -->

    <label for="step_message">Message</label>
    <textarea
      name="step_message"
      bind:value={selectedStep.message}
      type="text"
    />

    {#if selectedStep.action === "button_selection"}
      <label for="step_branches">Branches</label>
      {#each selectedStep.branches as branch}
        <div class="branch">
          <label for="">Message</label>
          <input type="text" bind:value={branch.text} />

          <label for="">Next stepID</label>
          <select bind:value={branch.next_step_id}>
            {#each stepIDs as id}
              <option>{id}</option>
            {/each}
          </select>

          <label for="">ActionID</label>
          <span>{branch.action_id}</span>
        </div>
      {/each}

      <input bind:value={nextStepID} type="text" placeholder="next_step_id" />
      <input bind:value={action} type="text" placeholder="action" />
      <button on:click={createWorkflowBranch}>New branch</button>
    {/if}

    <button on:click={updateWorkflow}>Save</button>
  </div>
</div>

<style>
  textarea {
    width: 100%;
    height: 5rem;
    padding: 12px 20px;
    box-sizing: border-box;
    border: 2px solid #ccc;
    border-radius: 4px;
    background-color: #f8f8f8;
    resize: none;
  }

  input,
  select {
    width: 100%;
  }

  .branch {
    margin: 2rem 0;
  }
</style>
