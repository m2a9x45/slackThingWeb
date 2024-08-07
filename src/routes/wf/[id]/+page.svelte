<script>
    import Draggable from "../../../components/draggable.svelte";
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

    let lines = {};

    function onMouseUp(e) {
        console.log(lines);
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
    <Draggable bind:lines>
        <h1>Drag Me</h1>
    </Draggable>
    <Draggable>
        <h1>Drag Me 2</h1>
    </Draggable>

    <svg width="500" height="500"
        ><line x1="50" y1="50" x2="350" y2="50" stroke="red" /></svg
    >
</main>

<svelte:window on:mouseup={onMouseUp} />

<style>
    main {
        width: 100%;
        height: 100%;
        margin: 1rem auto;
    }
</style>
