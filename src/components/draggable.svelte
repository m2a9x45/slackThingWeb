<script>
  import { get } from "svelte/store";
  import { lines } from "../routes/wf/[id]/linesStore";

  export let id;
  export let workflowID;
  export let left = 100;
  export let top = 100;
  //   export let lines;

  $: innerWidth = 0;
  $: innerHeight = 0;

  let moving = false;

  function handleMouseUp() {
    lines.update((currentLines) => {
      currentLines.set(id, { left: left, top: top });
      return currentLines;
    });

    saveStepLocation(Object.fromEntries(get(lines)));
  }

  function onMouseDown() {
    moving = true;
  }

  function onMouseMove(e) {
    if (moving) {
      left += e.movementX;
      top += e.movementY;

      if (top < 0) {
        top = 0;
      }

      if (left < 0) {
        left = 0;
      }

      // if (left > innerWidth - 100) {
      //   left = innerWidth - 100;
      // }

      // if (top > innerHeight - 75) {
      //   top = innerHeight - 75;
      // }
    }
  }

  async function saveStepLocation(locations) {
    const body = {
      wf_id: workflowID,
      locations,
    };

    const response = await fetch(`http://localhost:5000/wf/step/location`, {
      method: "POST",
      body: JSON.stringify(body),
      headers: {
        "Content-Type": "application/json",
      },
      credentials: "include",
    });
  }

  function onMouseUp(e) {
    moving = false;

    // lines.update((currentLines) => {
    //   currentLines.set(id, { left: left, top: top });
    //   return currentLines;
    // });

    // lines.set(id, { x: left, y: top });
    // lines = {
    //   id: id,
    //   x: left,
    //   y: top,
    // };
  }

  // 	$: console.log(moving);
</script>

<div
  on:mousedown={onMouseDown}
  on:mouseup={handleMouseUp}
  style="left: {left}px; top: {top}px;"
  class="draggable"
>
  <slot></slot>
</div>

<svelte:window
  on:mouseup={onMouseUp}
  on:mousemove={onMouseMove}
  bind:innerWidth
  bind:innerHeight
/>

<style>
  .draggable {
    max-width: 300px;
    user-select: none;
    cursor: move;
    border: solid 1px gray;
    position: absolute;
    padding: 1rem;
  }
</style>
