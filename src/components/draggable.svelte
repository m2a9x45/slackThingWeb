<script>
  import { lines } from "../routes/wf/[id]/linesStore";

  export let left = 100;
  export let top = 100;

  export let id;

  $: innerWidth = 0;
  $: innerHeight = 0;

  //   export let lines;

  let moving = false;

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

      if (left > innerWidth - 100) {
        left = innerWidth - 100;
      }

      if (top > innerHeight - 75) {
        top = innerHeight - 75;
      }
    }
  }

  function onMouseUp(e) {
    moving = false;

    lines.update((currentLines) => {
      currentLines.set(id, { x: left, y: top });
      return currentLines;
    });

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
    user-select: none;
    cursor: move;
    border: solid 1px gray;
    position: absolute;
    padding: 1rem;
  }
</style>
