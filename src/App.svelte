<script>
    const VIEW_TIMER = 0;
    const VIEW_LOG = 1;
    const VIEW_OPTIONS = 2;

    let CURRENT_VIEW = VIEW_TIMER;
    import Timer from './Timer.svelte';

    function setView(event) {
        console.log(arguments);
        CURRENT_VIEW = type;
    }
</script>

<h1>Work timer</h1>

<nav>
    <ul>
        <li><a href="#timer">Timer</a></li>
        <li><a href="#log">Log</a></li>
        <li><a href="#options">Options</a></li>
    </ul>
</nav>

<Timer />
