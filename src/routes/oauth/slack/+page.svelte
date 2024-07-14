<script>
    import { onMount } from "svelte";

    onMount(async () => {
        const urlParams = new URLSearchParams(window.location.search);
        const code = urlParams.get("code");
        console.log(code);

        const response = await fetch("http://localhost:5000/oauth/slack", {
            method: "POST",
            headers: {
                "content-type": "application/json",
            },
            body: JSON.stringify({ code: code }),
            credentials: "include",
        });
        const resData = await response.json();
        console.log(resData);
    });
</script>

<h1>Hello, Slack redirect</h1>
