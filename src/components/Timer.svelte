<script>
    export let time;
    export let text;
</script>

<style>
    .countdown-time {
        display: flex;
        flex-direction: column;
        border-radius: 50%;
        align-items: center;
        justify-content: center;
        min-width: 200px;
        max-width: 200px;
        min-height: 200px;
        max-height: 200px;
        margin: 20px;
    }

    .time {
        font-weight: bold;
        font-size: 3rem;
    }

    .text {
        color: grey;
    }

    .days {
        border: 10px solid red;
    }

    .hours {
        border: 10px solid yellow;
    }

    .minutes {
        border: 10px solid white;
    }

    .seconds {
        border: 10px solid blue;
    }
</style>

<div class='countdown-time {text}'>
    <span class='time'>{time}</span>
    <span class='text'>{text}</span>
</div>