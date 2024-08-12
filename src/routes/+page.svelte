<script>
    import tmi from "tmi.js";
    import { page } from "$app/stores";
    import { onMount } from "svelte";

    let Clicked = false;
    let count = 0;
    let channel = "defaultChannel";
    let bgColor = "#f39c12";
    let textColor = "white";

    // Extract URL parameters
    $: {
        const params = $page.url.pathname.split("/").slice(1);
        if (params.length >= 1) channel = params[0];
        if (params.length >= 2) bgColor = params[1];
        if (params.length >= 3) textColor = params[2];
    }

    // Initialize the Twitch client
    const client = new tmi.Client({
        channels: [channel],
    });

    // Connect to the Twitch channel
    onMount(() => {
        client.connect();
    });

    // Listen for messages in the Twitch chat
    client.on("message", (channel, tags, message) => {
        handleClick();
    });

    function handleClick() {
        count++;
        Clicked = false;
        setTimeout(() => {
            Clicked = true;
        }, 200); // Changed to 200ms for better visibility
    }
</script>

<!-- HTML structure for the animation -->
<div class="stream-overlay">
    <div
        class="circle"
        class:active={Clicked}
        style="background-color: {bgColor};"
    >
        <h1 class="text" style="color: {textColor};">{count}</h1>
    </div>
</div>

<!-- CSS for styling and animation -->
<style>
    .stream-overlay {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 300px;
        height: 300px;
        background-color: #1a1a1a;
        border-radius: 20px;
    }

    .circle {
        width: 200px;
        height: 200px;
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .text {
        font-family: "Lucida Sans", "Lucida Sans Regular", "Lucida Grande",
            "Lucida Sans Unicode", Geneva, Verdana, sans-serif;
        font-size: 50px;
        text-align: center;
    }

    @keyframes clickAnimation {
        0% {
            transform: scale(1);
        }
        50% {
            transform: scale(1.5);
        }
        100% {
            transform: scale(1);
        }
    }

    .active {
        animation: clickAnimation 0.2s ease-in-out;
    }
</style>
