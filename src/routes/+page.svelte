<script>
    import { base } from "$app/paths";
    import { eventName } from "$lib/event";
    import { page } from "$app/state";
    import { onMount } from "svelte";
    import { checkCity, proccessCity } from "$lib/event";

    import { slide } from "svelte/transition";

    let city = $state("");
    let isValid = $state(null);
    onMount(async () => {
        let urlParams = new URLSearchParams(window.location.search);
        if (urlParams.get("code") != null) {
            sessionStorage.setItem("authCode", urlParams.get("code"))
            window.location.href = base + "/" + sessionStorage.getItem("refAuthCity") + "/control/?liveshare=true" 
            return;
        }
        let extractedCity = urlParams.get('eventName');
        if (urlParams.get("eventName") == null) {
            isValid = false;
        }

        if (extractedCity !== null) {
            let processed = proccessCity(extractedCity);
            
            let result = await checkCity(processed);

            console.log("Processed City:", processed);
            console.log("Is Valid Check (actual boolean):", result);

            isValid = result; 
            city = extractedCity;
        }
    })

    let inputCity = $state("")

    function cleanedCity(given) {
        let rtn = "";
        for (let i = 0; i < given.length-1; i++) {
            if (given.substring(i, i+1) == " ") {
                rtn += "-";
            }
            else {
                rtn += given.substring(i, i+1);
            }
        }
        rtn += given.substring(given.length-1);
        return rtn;
    }
</script>
<style>
    * {
        margin-left: 30px;
        margin-right: 30px;
    }
    a {
        margin: 0;
    }
    div {
        background-color: rgb(213, 213, 213);
        padding: 20px;
        margin: 20px;
        border-radius: 20px;
        h3 {
            margin-top: 20px;
        }
        h4 {
            margin-top: 10px;
            margin-bottom: 20px;
        }
    }
</style>
<svelte:head>
    <title>Jumbotron</title>
</svelte:head>

<h1>Welcome to {eventName} Jumbotron</h1>
<h2>Organizer Landing Page</h2>
<p style:margin-bottom=50px>Not an organizer? You can head to <a href="https://{eventName.toLowerCase()}.hackclub.com">{eventName.toLowerCase()}.hackclub.com</a> and find your city to get the appropriate Liveshare link!</p>
<div>
    <h3>Event Name</h3>
    {#if isValid == true}
    <h4>Your event is currently configured for {proccessCity(city)}</h4>
    {:else if isValid == null}
    <p>Loading...</p>
    {:else}
    <h4>You currently have no event configured. Your provided name may not have been in an accepted format.<br>Please use the link provided from Cockpit for Jumbotron</h4>
    <form onsubmit={() => {window.location.href = base + "/?eventName=" + cleanedCity(inputCity)}}>
        <p>You can also try to search for your event by entering the name</p>
        <input bind:value={inputCity} placeholder="ie. leafyland" type="text">
    </form>
    {/if}
</div>
{#if isValid == true}
<div transition:slide>
    <h3>Login with Hack Club OAuth</h3>
    <p>Logging in is optional; however, Liveshare functionality for your event will not work</p>
    <p><button onclick={() => {sessionStorage.setItem("refAuthCity", city); window.location.href = "https://auth.hackclub.com/oauth/authorize?client_id=e86d4d7eec9c546e6c4700388d4fea7f&redirect_uri=https%3A%2F%2Fcurly-bassoon-6vrjgqpjpjjhwj6-5173.app.github.dev%2Fjumbotron%2F&response_type=code&scope=verification_status+slack_id"}}>Log in</button></p>
    <p><a href="{base}/{city}/control/" style:color="black">Continue without OAuth</a></p>
</div>
{/if}
