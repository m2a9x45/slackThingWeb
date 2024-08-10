<script>
  import { page } from "$app/stores";

  import Draggable from "../../../components/draggable.svelte";
  import Inspector from "../../../components/inspector.svelte";
  import NewStep from "../../../components/newStep.svelte";

  import { onMount } from "svelte";

  // import { get } from "svelte/store";
  // import { lines } from "./linesStore";

  const wf_id = $page.params.id;
  console.log(wf_id);

  onMount(() => {
    readWorkflow(wf_id);
  });

  export let steps = [];
  let stepIDs = [];

  let showSidebar = false;

  // let x1;
  // let x2;
  // let y1;
  // let y2;

  async function callbackFunction(event) {
    console.log(`Notify fired! Detail: ${event.detail}`);
    await readWorkflow(wf_id);

    if (event.detail != "default") {
      let foundStep = steps.find((step) => step.step_id === event.detail);
      console.log(foundStep);
      selectedStep = foundStep;
    }
  }

  async function readWorkflow(wf_id) {
    const response = await fetch(`http://localhost:5000/wf/${wf_id}`, {
      credentials: "include",
    });
    const data = await response.json();
    console.log(data);
    steps = data;

    stepIDs = [];
    steps.forEach((step) => {
      stepIDs.push(step.step_id);
    });
  }

  function onMouseUp(e) {
    // console.log(get(lines));
    //   const things = get(lines);
    //   x1 = things.get("123").x;
    //   y1 = things.get("123").y;
    //   x2 = things.get("456").x;
    //   y2 = things.get("456").y;
  }

  let selectedStep;
  function editStep(step) {
    showSidebar = true;
    selectedStep = step;
  }
</script>

<main>
  <div class="content">
    <NewStep on:notify={callbackFunction} {wf_id}></NewStep>

    {#each steps as step, i}
      <Draggable id={step.step_id} top={100 + i * 300}>
        <div>
          <h4>StepID: {step.step_id}</h4>
          <p>Type: {step.action}</p>
          <p>Message: {step.message}</p>
          <div>
            <ul>
              {#each step.branches as branch}
                <li>{branch.text} -> {branch.next_step_id}</li>
              {/each}
            </ul>
          </div>
          <button on:click={editStep(step)}>Edit</button>
        </div>
      </Draggable>
    {/each}

    <!-- <Draggable id={"123"} top={400}>
      <h1>Drag Me</h1>
    </Draggable>
    <Draggable id={"456"} left={400} top={400}>
      <h1>Drag Me 2</h1>
    </Draggable> -->

    <!-- <svg><line {x1} {y1} {x2} {y2} stroke="red" /></svg> -->
  </div>

  {#if showSidebar}
    <div class="sidebar">
      <h1>Step editor</h1>
      <Inspector on:notify={callbackFunction} step={selectedStep} {stepIDs}
      ></Inspector>
    </div>
  {/if}
</main>

<svelte:window on:mouseup={onMouseUp} />

<style>
  :global(body) {
    width: 100vw;
    height: 100vh;
    margin: 0;
  }

  main {
    display: flex;
    width: 100%;
    height: 100%;
  }

  .content {
    width: 90vw;
  }

  .sidebar {
    padding: 1rem;
    width: 30%;
    max-width: 500px;
    height: 100vh;
    background-color: rgb(206, 231, 246);
  }

  svg {
    width: 100%;
    height: 100%;
  }
</style>
