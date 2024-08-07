<script>
  import Draggable from "../../../components/draggable.svelte";
  import { get } from "svelte/store";
  import { lines } from "./linesStore";
  // import { onMount } from "svelte";

  // onMount(() => {
  //     readWorkflow();
  // });

  // export let steps = [];

  // async function readWorkflow() {
  //     const response = await fetch(`http://localhost:5000/wf`, {
  //         credentials: "include",
  //     });
  //     const data = await response.json();
  //     console.log(data);
  //     steps = data;
  // }

  let x1;
  let x2;
  let y1;
  let y2;

  function onMouseUp(e) {
    console.log(get(lines));
    // console.log(get(lines).get("123").x);

    const things = get(lines);

    x1 = things.get("123").x;
    y1 = things.get("123").y;

    x2 = things.get("456").x;
    y2 = things.get("456").y;
  }
</script>

<!-- {#each steps as step}
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
    </div>
{/each} -->

<!-- https://stackoverflow.com/questions/64604624/programatically-get-svelte-component-instance -->

<main>
  <Draggable id={"123"}>
    <h1>Drag Me</h1>
  </Draggable>
  <Draggable id={"456"}>
    <h1>Drag Me 2</h1>
  </Draggable>

  <svg><line {x1} {y1} {x2} {y2} stroke="red" /></svg>
</main>

<svelte:window on:mouseup={onMouseUp} />

<style>
  :global(body) {
    width: 100vw;
    height: 100vh;
  }

  main {
    width: 100%;
    height: 100%;
    margin: 1rem auto;
  }

  svg {
    width: 100%;
    height: 100%;
  }
</style>
