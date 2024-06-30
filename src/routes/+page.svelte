<script>
    import { onMount } from "svelte";

    async function onSubmit(e) {
        const formData = new FormData(e.target);

        const data = {};
        for (let field of formData) {
            const [key, value] = field;
            data[key] = value;
        }

        const response = await fetch("http://localhost:5000/slash", {
            method: "POST",
            headers: {
                "content-type": "application/json",
            },
            body: JSON.stringify(data),
        });
        const resData = await response.json();
        console.log(resData);

        fetchCommands();
    }

    export let commands = [];

    onMount(() => {
        fetchCommands();
    });

    async function fetchCommands() {
        const response = await fetch(`http://localhost:5000/slash`);
        const data = await response.json();
        console.log(data);
        commands = data;
    }
</script>

<main>
    <form on:submit|preventDefault={onSubmit}>
        <label for="">Escalation ID</label>
        <input name="path_id" type="text" placeholder="path_id" />
        <label for="">Slash command</label>
        <input name="command" type="text" placeholder="slash command" />
        <label for="">Workflow ID</label>
        <input name="workflow_id" type="text" placeholder="workflow_id" />
        <input type="submit" value="submit" />
    </form>

    <h3>Current commands</h3>

    <div>
        <ul>
            {#each commands as command}
                <li>
                    {command.path_id}, {command.command}, {command.workflow_id}
                </li>
            {/each}
        </ul>
    </div>
</main>

<style>
    main {
        width: 45%;
        margin: 5rem auto;
    }

    input[type="text"] {
        width: 100%;
        padding: 1em;
        margin: 0.5em 0;
        box-sizing: border-box;
    }

    input[type="submit"] {
        background-color: #243f90;
        border: none;
        color: white;
        padding: 15px 32px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-size: 16px;
    }
</style>
