<script>
    import tmi from "tmi.js";
    import { page } from "$app/stores";
    import { onMount } from "svelte";
    import { get } from "svelte/store";

    let channel = get(page).params.slug;
    let Clicked = false;
    let count = 0;
    let bgColor = "black";
    let textColor = "white";

    // Initialize the Twitch client
    const client = new tmi.Client({
        channels: [channel],
    });

    // Connect to the Twitch channel
    onMount(() => {
        client.connect().catch(console.error);
    });

    // Listen for messages in the Twitch chat
    client.on("message", () => {
        handleClick();
    });

    // Error handling for connection issues
    client.on("connected", () => {});
    client.on("disconnected", () => {});
    client.on("error", () => {});

    function handleClick() {
        count++;
        Clicked = false;
        setTimeout(() => {
            Clicked = true;
        }, 100); // Changed to 200ms for better visibility
    }

    function formatCount(count) {
        if (count >= 1e9) {
            return (count / 1e9).toFixed(2) + "B";
        } else if (count >= 1e6) {
            return (count / 1e6).toFixed(2) + "M";
        } else if (count >= 1e3) {
            return (count / 1e3).toFixed(2) + "K";
        } else {
            return count.toFixed(0);
        }
    }
</script>

<!-- HTML structure for the animation -->
<div class="stream-overlay">
    <div
        class="circle"
        class:active={Clicked}
        style="background-color: {bgColor};"
    >
        <h1 class="text" style="color: {textColor};">{formatCount(count)}</h1>
    </div>
</div>

<!-- CSS for styling and animation -->
<style>
    .stream-overlay {
        position: fixed;
        top: 50px;
        left: 50px;
        display: flex;
        justify-content: center;
        align-items: center;
        width: 300px;
        height: 300px;
    }

    .circle {
        width: 250px;
        height: 250px;
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        box-shadow: 0 0 50px rgba(255, 255, 255, 0.5);
    }

    .text {
        font-family: "Lucida Sans", "Lucida Sans Regular", "Lucida Grande",
            "Lucida Sans Unicode", Geneva, Verdana, sans-serif;
        font-size: 50px;
        text-align: center;
        text-shadow: 0 0 5px white;
    }

    @keyframes clickAnimation {
        0% {
            transform: scale(1);
        }
        50% {
            transform: scale(1.25);
        }
        100% {
            transform: scale(1);
        }
    }

    .active {
        animation: clickAnimation 0.1s ease-in-out;
    }
</style>
