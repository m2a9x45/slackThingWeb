<script>
  import { page } from "$app/stores";
  import { onMount } from "svelte";
  // import { get } from "svelte/store";
  // import { lines } from "./linesStore";

  import Draggable from "../../../components/draggable.svelte";
  import Inspector from "../../../components/inspector.svelte";
  import NewStep from "../../../components/newStep.svelte";

  export let steps = [];

  const wf_id = $page.params.id;

  let stepIDs = [];
  let showSidebar = false;
  let selectedStep;
  // let x1;
  // let x2;
  // let y1;
  // let y2;

  onMount(() => {
    readWorkflow(wf_id);
    readStepsLocations(wf_id);
  });

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

  const stepLocations = new Map();

  async function readStepsLocations(wf_id) {
    const response = await fetch(
      `http://localhost:5000/wf/step/location/${wf_id}`,
      {
        credentials: "include",
      }
    );
    const data = await response.json();
    console.log(data);

    data.forEach((step) => {
      stepLocations.set(step.step_id, { left: step.left, top: step.top });
    });
  }

  function onMouseUp(e) {
    // console.log(get(lines));
    // const things = get(lines);
    // x1 = things.get("123").x;
    // y1 = things.get("123").y;
    // x2 = things.get("456").x;
    // y2 = things.get("456").y;
  }

  function editStep(step) {
    showSidebar = true;
    selectedStep = step;
  }

  function getStepLocation(stepID, i, direction) {
    const locaiton = stepLocations.get(stepID);
    if (locaiton === undefined) {
      return 100 + i * 300;
    }

    if (direction === "top") {
      return locaiton.top;
    } else {
      return locaiton.left;
    }
  }
</script>

<main>
  <div class="content">
    <NewStep on:notify={callbackFunction} {wf_id}></NewStep>

    {#each steps as step, i}
      <Draggable
        id={step.step_id}
        workflowID={step.wf_id}
        top={getStepLocation(step.step_id, i, "top")}
        left={getStepLocation(step.step_id, i, "left")}
      >
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
    height: 200vh;
    background-color: rgb(244, 244, 244);
  }

  svg {
    width: 100%;
    height: 100%;
  }
</style>
