<script>
    import { onMount } from 'svelte';

    const VIEW_TIMER = 0;
    const VIEW_LOG = 1;
    const VIEW_OPTIONS = 2;

    let NOTIFICATION_DENIED = false;
    let NOTIFICATION_ALLOWED = false;
    let DARK_MODE = true;

    let CURRENT_VIEW = VIEW_TIMER;
    import Timer from './Timer.svelte';

    function setView(event) {
        console.log(arguments);
        CURRENT_VIEW = type;
    }

    function toggleColor() {
        window.document.body.classList.toggle('light');
        DARK_MODE = !DARK_MODE;
    }

    function requestNotificationPerms() {
        Notification.requestPermission().then(function (permission) {
            // If the user accepts, let's create a notification
            if (permission === "granted") {
                NOTIFICATION_ALLOWED = true
                var notification = new Notification("Hi there!");
            }
        });
    }

    onMount(async () => {
        switch (Notification.permission) {
            case "granted":
                NOTIFICATION_ALLOWED = true;
                break;
            case "denied":
                NOTIFICATION_DENIED = true;
                break;
            default:
                requestNotificationPerms();
        }
    });
</script>

<style>
:root {
    --bg-color: #000000;
    --text-color: #FFFFFF;
    --anchor-color: #fa2e2e;
    --anchor-visited-color: #ea0606;
}
:global(body, textarea, button, input) {
    background: var(--bg-color);
    color: var(--text-color);
}
:global(a) {
    color: var(--anchor-color);
}
:global(a:visited) {
    color: var(--anchor-visited-color);
}
:global(body.light) {
    --bg-color: #FFFFFF;
    --text-color: #000000;
}

h1 {
    display: inline-block;
    margin-right: 1rem;
}

nav {
    position: absolute;
    bottom: 0.25rem;
    left: 1rem;
}

nav ul li, nav ul {
    padding: 0;
    margin: 0;
    list-style: none;
}

nav ul {
    display: inline-block;
}

nav ul li {
    font-size: 1.1em;
    display: inline-block;
    padding-right: 1rem;
}

.vcenter {
    position: absolute;
    top: 40%;
    transform: translate(-50%, -50%);
    left: 50%;
}
</style>


<nav>
    <h1>Work timer</h1>
    <ul>
        <li><a href="#timer">Timer</a></li>
        <li><a href="#log">Log</a></li>
        <li><a href="#options">Options</a></li>
        <li>·</li>
        <li><a href="#darkmode" on:click|preventDefault={toggleColor}>{ DARK_MODE ? 'Go light' : 'Go dark' }</a></li>
        <li>
            {#if NOTIFICATION_ALLOWED}
                Notifications Enabled
            {:else if NOTIFICATION_DENIED}
                Notifications Denied (check site settings)
            {:else}
                <a href="#notifications" on:click={requestNotificationPerms}>
                    Enable Notifications
                </a>
            {/if}
        </li>
    </ul>
</nav>

<div class="vcenter">
    <Timer />
</div>
