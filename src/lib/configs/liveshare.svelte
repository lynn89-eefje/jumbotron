<script>
    import { onMount } from "svelte";
    import { liveshareData, openGate, startAPI, updateAPI } from "$lib/liveshare.svelte.js";
    import {eventName} from "$lib/event.js";

    let ping;

    let paused = $state(false);

    let cityName = $state("");
    onMount(() => {
        cityName = sessionStorage.getItem("refAuthCity").toLowerCase();
        if (sessionStorage.getItem("liveshareKey") == null || sessionStorage.getItem("liveshareKey") == "" || sessionStorage.getItem("liveshareEmail") == null || sessionStorage.getItem("liveshareEmail") == "") {
            return;
        }  
        startAPI();

        window.addEventListener("pagehide", sendDisconnectBeacon);
    })

    function sendDisconnectBeacon() {
        // 1. Removed the 'openGate' check. 
        // If the tab is closing, we force the 'active: false' status regardless of local UI state.
        
        const url = "https://jumbotron.lynn89sudo.hackclub.app/mutate";
        
        // 2. Be extremely explicit with the payload structure
        const payload = JSON.stringify({
            auth: {
                emailAddress: sessionStorage.getItem("liveshareEmail"), 
                key: sessionStorage.getItem("liveshareKey")
            },
            cityName: cityName, 
            data: { 
                active: false    
            }
        });
        
        const blob = new Blob([payload], { type: "application/json" });
        
        // 3. sendBeacon returns a boolean. If it returns false, 
        // the browser failed to queue the request.
        const success = navigator.sendBeacon(url, blob);
        console.log("Beacon queued:", success); 
    }

    function enableLive() {
        if (sessionStorage.getItem("liveshareKey") == null || sessionStorage.getItem("liveshareKey") == "" || sessionStorage.getItem("liveshareEmail") == null || sessionStorage.getItem("liveshareEmail") == "") {
            return;
        }  
        openGate.isOpen = true;
        openGate.paused = false;
        paused = false;
        liveshareData.active = true;
        updateAPI();
        setTimeout(() => {sendDisconnectBeacon()}, 6000);
    }

    function destroy() {
        liveshareData.active = false
        updateAPI();
        openGate.isOpen = false;
        openGate.paused = false;
        paused = false;
    }
    function pause() {
        openGate.isOpen = false;
        openGate.paused = true;
        paused = true;
    }
    function unpause() {
        openGate.isOpen = true;
        openGate.paused = false;
        updateAPI();
        paused = false;
    }
</script>
<style>
    button {
        background-color: rgb(92, 89, 89);
    }
</style>

{#if !openGate.isOpen && paused == false}
<div>
    <p>Show participants displayed content on their own screens</p>
    <p><button class="bigButton" onclick={enableLive}><span translate="no" title="Enable Liveshare" class="material-symbols-outlined">cast</span></button></p>
</div>
{:else}
<p>Liveshare is enabled. Participants can head to <a href="https://jumbotron.hackclub.com/{cityName}">jumbotron.hackclub.com/{cityName}</a> or find the link on your event's homepage at <a href="https://{eventName.toLowerCase()}.hackclub.com">{eventName.toLowerCase()}.hackclub.com</a></p>
<p>{#if !openGate.paused}<button onclick={pause}>Pause Liveshare</button>{:else}<button onclick={unpause}>Unpause Liveshare</button>{/if} <button onclick={destroy}>End Liveshare</button></p>
{#if openGate.paused}<p><i>Liveshare will still display as active to participants while paused; data will not be update</i>d</p>{/if}
{/if}
