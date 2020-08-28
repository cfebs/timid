<script>
    const VIEW_TIMER = 0;
    const VIEW_LOG = 1;
    const VIEW_OPTIONS = 2;

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
    transform: translateY(-50%);
}
</style>


<nav>
    <h1>Work timer</h1>
    <ul>
        <li><a href="#timer">Timer</a></li>
        <li><a href="#log">Log</a></li>
        <li><a href="#options">Options</a></li>
        <li><a href="#" on:click={toggleColor}>{ DARK_MODE ? 'Light mode' : 'Dark mode' }</a></li>
    </ul>
</nav>

<div class="vcenter">
    <Timer />
</div>
