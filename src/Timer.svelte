<script>
    const COUNTDOWN_STATE_STOP = 0;
    const COUNTDOWN_STATE_PAUSE = 1;
    const COUNTDOWN_STATE_RUN = 2;
    const COUNTDOWN_STATE_WAITING = 3;

    const COUNTDOWN_TYPE_WORK = 0;
    const COUNTDOWN_TYPE_BREAK = 1;

    const breakSeconds = 5 * 60;
    const workSeconds = 35 * 60;
    // const breakSeconds = 5;
    // const workSeconds = 10;

    let COUNTDOWN_TYPE = COUNTDOWN_TYPE_WORK;
    let COUNTDOWN_STATE = COUNTDOWN_STATE_STOP;

    let notesValue = "";

    let countdownSeconds = workSeconds;
    let remainingSeconds = countdownSeconds;
    let timerInterval;
    let waitInterval;
    $: remainingSecondsInTime = secondsToTime(remainingSeconds)

    let logEntries = [];
    const numLastLogEntries = 10;
    $: lastLogEntries = logEntries.slice(-1 * numLastLogEntries).reverse();

    function padZeros(n) {
        if (n >= 10) {
            return '' + n;
        }

        return '0' + n;
    }

    function secondsToTime(seconds) {
        const hours = Math.floor(seconds / 3600);
        seconds %= 3600;
        const minutes = Math.floor(seconds / 60);
        const s = seconds % 60;
        return `${padZeros(hours)}:${padZeros(minutes)}:${padZeros(s)}`
    }

    function startTimer() {
        clearInterval(timerInterval);
        clearInterval(waitInterval);

        if (COUNTDOWN_STATE !== COUNTDOWN_STATE_PAUSE) {
            remainingSeconds = countdownSeconds;
        }

        COUNTDOWN_STATE = COUNTDOWN_STATE_RUN
        remainingSeconds -= 1;
        timerInterval = setInterval(() => {
            remainingSeconds -= 1;
            if (remainingSeconds === 0) {
                clearInterval(timerInterval);
                COUNTDOWN_STATE = COUNTDOWN_STATE_WAITING;
                wait()
            }
        }, 1000);
    }

    function pause() {
        clearInterval(timerInterval);
        COUNTDOWN_STATE = COUNTDOWN_STATE_PAUSE;
    }

    function notify(msg) {
        if (Notification.permission === "granted") {
            var notification = new Notification(msg);
            return notification;
        }
    }

    function wait() {
        clearInterval(waitInterval)
        let msg = ""

        if (COUNTDOWN_TYPE === COUNTDOWN_TYPE_BREAK) {
            msg = "Timer: Break done! Waiting to start work stage.";
        }
        else if (COUNTDOWN_TYPE === COUNTDOWN_TYPE_WORK) {
            msg = "Timer: Work done! Waiting to start break stage.";
        }

        notify(msg);
        waitInterval = setInterval(() => {
            notify(msg);
        }, 15000);
    }

    function createLog() {
        console.log('Creating log', notesValue);
        const logEntry = {
            date: new Date(),
            countdown_type: COUNTDOWN_TYPE,
            notes: notesValue,
            total_seconds: countdownSeconds - remainingSeconds,
        };

        logEntries.push(logEntry);
        logEntries = logEntries;
    }

    function nextStage() {
        createLog()
        if (COUNTDOWN_TYPE === COUNTDOWN_TYPE_WORK) {
            console.log('Going into break for', breakSeconds);
            countdownSeconds = breakSeconds;
            COUNTDOWN_TYPE = COUNTDOWN_TYPE_BREAK;
            startTimer();
            return;
        }

        if (COUNTDOWN_TYPE === COUNTDOWN_TYPE_BREAK) {
            console.log('Going into work for', workSeconds);
            countdownSeconds = workSeconds;
            COUNTDOWN_TYPE = COUNTDOWN_TYPE_WORK;
            startTimer();
            return;
        }

        throw new Error('Invalid stage state');
    }
</script>
<style>
    .timer {
        width: 800px;
        text-align: center;
        margin: 0 auto;
    }
    .log {
        width: 800px;
        margin: 0 auto;
    }
    textarea {
        width: 80%;
        display: block;
        margin: 1em auto;
    }
    time {
        font-size: 15vh;
        font-family: monospace;
    }
</style>

<div class="timer">
    <time datetime={remainingSecondsInTime}>
        {remainingSecondsInTime}
    </time>

    <div>
        {#if COUNTDOWN_TYPE === COUNTDOWN_TYPE_WORK}
            Work
        {:else if COUNTDOWN_TYPE === COUNTDOWN_TYPE_BREAK}
            Break
        {/if}
        -
        {#if COUNTDOWN_STATE === COUNTDOWN_STATE_PAUSE}
            Paused
        {:else if COUNTDOWN_STATE === COUNTDOWN_STATE_RUN}
            Running
        {:else if COUNTDOWN_STATE === COUNTDOWN_STATE_WAITING}
            Waiting
        {:else if COUNTDOWN_STATE === COUNTDOWN_STATE_STOP}
            Waiting
        {/if}
    </div>

    <textarea placeholder="Notes for this session" rows={5} bind:value={notesValue}></textarea>

    <button on:click={startTimer}>
        {#if COUNTDOWN_STATE === COUNTDOWN_STATE_PAUSE}
            Resume timer
        {:else if COUNTDOWN_STATE === COUNTDOWN_STATE_RUN}
            Restart timer
        {:else}
            Start Timer
        {/if}
    </button>
    <button on:click={nextStage}>
        {#if COUNTDOWN_STATE === COUNTDOWN_STATE_WAITING}
            Next stage
        {:else}
            Skip stage
        {/if}
    </button>
    <button on:click={pause} disabled={COUNTDOWN_STATE !== COUNTDOWN_STATE_RUN}>Pause</button>
</div>
<div class="log">
    <h3>Last {numLastLogEntries} log entries</h3>
    <ul>
    {#each lastLogEntries as log}
        <li>{log.date} - {log.notes}</li>
    {:else}
        <li>No logs yet, complete some stages first.</li>
    {/each}
    </ul>
</div>
