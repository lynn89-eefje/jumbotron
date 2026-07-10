<script>
    import { eventName } from "$lib/event.js";
    import { checkCity, proccessCity } from "$lib/event.js";
    import { page } from "$app/state";
    import { onMount } from "svelte";

    import { blur } from "svelte/transition";

    let cityLink = "";
     let boxes = $state({})

    let p2 = $state("We're checking if Liveshare is enabled for this event.");
    onMount(async function() {
        console.log("Checking city name...")
     try {
        if (await checkCity(proccessCity(page.params.city)) == true) {
            console.log("City name is valid!")
            cityLink = await proccessCity(page.params.city);

        }
        else {
            p2 = "No liveshare session was found for this event. If you believe this is a mistake, please see an organizer.";
            return;
        }
     }
     catch(err) {
        p2 = "No liveshare session was found for this event. If you believe this is a mistake, please see an organizer.";
        return;
     }

    

     let eventSource = new EventSource(`https://jumbotron.lynn89sudo.hackclub.app/stream?cityName=${proccessCity(page.params.city)}`);
     eventSource.onmessage = (event) => {
        const parsed = JSON.parse(event.data);
        if (parsed && !parsed.message) {
            boxes = parsed;
            console.log("Stream updated", boxes);
            if (boxes.active === false) {
                p2 = "Liveshare is no longer enabled for this event; it is available, and can be enabled by an organizer. You can keep this page open while you wait.";
                boxes = {};
            }
            else {
                if (p2 !== "Liveshare is enabled. You should see active notifications for events, presentations, videos, and announcements below.") {
                    p2 = "Liveshare is enabled. You should see active notifications for events, presentations, videos, and announcements below.";
                }
            }
        }
     }

      try {
        let raw = await fetch(`https://jumbotron.lynn89sudo.hackclub.app/data?eventName=${cityLink}`);
        let liveshare = await raw.json();
        console.log(liveshare);
        if (liveshare.data.active == true) {
            p2 = "Liveshare is enabled. You should see active notifications for events, presentations, videos, and announcements below.";
            boxes = liveshare.data;
        }
        else {
            p2 = "Liveshare is not currently enabled for this event; it is available, and can be enabled by an organizer. Reload this page once an organizer activates Liveshare.";
        }
     }
     catch(err) {
        p2 = "Your event was found; however, there was an error with finding the Liveshare information. See an organizer if you need Liveshare.";
     }
    })

</script>
<style>
    @media screen and (min-width: 900px) {
        p {
            margin-left: 60px;
            margin-right: 60px;
        }
    }
    .box {
        background-color: lightgrey;
        padding: 10px;
        border-radius: 20px;
        border: darkgrey solid 5px;
        margin: 50px;
        padding-top: 20px;
    }
</style>
<svelte:head>
    <title>{eventName} Liveshare</title>
</svelte:head>
<h1><span style:font-size=25px style:font-weight=800>Welcome to</span> <br><span>{eventName} {proccessCity(page.params.city)}</span></h1>
<h2 style:font-family="Atkinson Hyperlegible Mono, Monserrat" style:font-size=20px>Liveshare Portal</h2>
<div aria-live="polite" aria-atomic="true">
    <p><output>{p2}</output></p>
    <p><button onclick={window.location.href = `https://${eventName.toLowerCase()}.hackclub.com`}>Return to {eventName} website</button></p>
</div>
<div aria-live="polite" aria-atomic="true">
    {#if boxes.announcement}
    <div class="box" transition:blur>
        <h3><output>Announcement: {boxes.announcement.title}</output></h3>
        {#if boxes.announcement.message && boxes.announcement.message !== ""}
        <p style:margin-top=20px><output>{boxes.announcement.message}</output></p>
        {:else}
        <p><i>No message is attached to this announcement</i></p>
        {/if}
    </div>
    {/if}
    {#if boxes.event}
    <div class="box" transition:blur>
        <h3><output>Event at {boxes.event.time}</output></h3>
        <p style:margin-top=20px><output>{boxes.event.title}</output></p>
    </div>
    {/if}
</div>