<script>
    import { onMount } from "svelte";

    const redirectURI = "https://7d7b-31-53-104-139.ngrok-free.app/oauth/slack";

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
        whoami();
    });

    async function fetchCommands() {
        const response = await fetch(`http://localhost:5000/slash`, {
            credentials: "include",
        });
        const data = await response.json();
        console.log(data);
        commands = data;
    }

    async function whoami() {
        const response = await fetch(`http://localhost:5000/whoami`, {
            credentials: "include",
        });
        const data = await response.json();
        console.log(data);
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

    <div>
        <a
            href="https://slack.com/openid/connect/authorize?scope=openid%20email%20profile&amp;response_type=code&amp;redirect_uri={redirectURI}&amp;client_id=7182068826032.7161902500340"
            style="align-items:center;color:#000;background-color:#fff;border:1px solid #ddd;border-radius:4px;display:inline-flex;font-family:Lato, sans-serif;font-size:16px;font-weight:600;height:48px;justify-content:center;text-decoration:none;width:256px"
            ><svg
                xmlns="http://www.w3.org/2000/svg"
                style="height:20px;width:20px;margin-right:12px"
                viewBox="0 0 122.8 122.8"
                ><path
                    d="M25.8 77.6c0 7.1-5.8 12.9-12.9 12.9S0 84.7 0 77.6s5.8-12.9 12.9-12.9h12.9v12.9zm6.5 0c0-7.1 5.8-12.9 12.9-12.9s12.9 5.8 12.9 12.9v32.3c0 7.1-5.8 12.9-12.9 12.9s-12.9-5.8-12.9-12.9V77.6z"
                    fill="#e01e5a"
                ></path><path
                    d="M45.2 25.8c-7.1 0-12.9-5.8-12.9-12.9S38.1 0 45.2 0s12.9 5.8 12.9 12.9v12.9H45.2zm0 6.5c7.1 0 12.9 5.8 12.9 12.9s-5.8 12.9-12.9 12.9H12.9C5.8 58.1 0 52.3 0 45.2s5.8-12.9 12.9-12.9h32.3z"
                    fill="#36c5f0"
                ></path><path
                    d="M97 45.2c0-7.1 5.8-12.9 12.9-12.9s12.9 5.8 12.9 12.9-5.8 12.9-12.9 12.9H97V45.2zm-6.5 0c0 7.1-5.8 12.9-12.9 12.9s-12.9-5.8-12.9-12.9V12.9C64.7 5.8 70.5 0 77.6 0s12.9 5.8 12.9 12.9v32.3z"
                    fill="#2eb67d"
                ></path><path
                    d="M77.6 97c7.1 0 12.9 5.8 12.9 12.9s-5.8 12.9-12.9 12.9-12.9-5.8-12.9-12.9V97h12.9zm0-6.5c-7.1 0-12.9-5.8-12.9-12.9s5.8-12.9 12.9-12.9h32.3c7.1 0 12.9 5.8 12.9 12.9s-5.8 12.9-12.9 12.9H77.6z"
                    fill="#ecb22e"
                ></path></svg
            >Sign in with Slack</a
        >
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
