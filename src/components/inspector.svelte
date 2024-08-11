<script>
  import { createEventDispatcher } from "svelte";

  export let step;
  export let stepIDs;

  const dispatch = createEventDispatcher();
  let selectedStep;
  let nextStepID;
  let action;
  let showAddOptions = false;

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

  function toggleShowAddOptions() {
    showAddOptions = !showAddOptions;
  }
</script>

<div class="content">
  <h3>Edit step - {selectedStep.step_id}</h3>
  <div>
    <label for="step_message">Message</label>
    <textarea
      name="step_message"
      bind:value={selectedStep.message}
      type="text"
    />

    {#if selectedStep.action === "button_selection"}
      <div>
        {#each selectedStep.branches as branch, i}
          <div class="branch">
            <label for="">Option {i + 1}:</label>
            <input type="text" bind:value={branch.text} />

            <label for="">StepID:</label>
            <select bind:value={branch.next_step_id}>
              {#each stepIDs as id}
                <option>{id}</option>
              {/each}
            </select>

            <!-- <label for="">ActionID</label> -->
            <!-- <span>{branch.action_id}</span> -->
          </div>
        {/each}

        <button on:click={toggleShowAddOptions}>Add Option</button>

        {#if showAddOptions}
          <input
            bind:value={nextStepID}
            type="text"
            placeholder="next_step_id"
          />
          <input bind:value={action} type="text" placeholder="action" />
          <button on:click={createWorkflowBranch}>New branch</button>
        {/if}
      </div>
    {/if}

    <button on:click={updateWorkflow}>Save</button>
  </div>
</div>

<style>
  .content {
    position: sticky;
    top: 0;
  }

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

  select {
    width: 100%;
  }

  input[type="text"] {
    width: 100%;
    padding: 12px 20px;
    margin: 8px 0;
    box-sizing: border-box;
  }

  button {
    width: 100%;
    margin: 0.5em 0;
    background-color: #3f9cff; /* Green */
    border: none;
    color: white;
    padding: 15px 32px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
  }

  .branch {
    margin: 1rem 0;
  }
</style>
