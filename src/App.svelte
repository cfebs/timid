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
}
:global(body, textarea, button, input) {
    background: var(--bg-color);
    color: var(--text-color);
}
:global(body.light) {
    --bg-color: #FFFFFF;
    --text-color: #000000;
}
</style>

<h1>Work timer</h1>

<nav>
    <ul>
        <li><a href="#timer">Timer</a></li>
        <li><a href="#log">Log</a></li>
        <li><a href="#options">Options</a></li>
        <li><a href="#" on:click={toggleColor}>{ DARK_MODE ? 'Light mode' : 'Dark mode' }</a></li>
    </ul>
</nav>

<Timer />
