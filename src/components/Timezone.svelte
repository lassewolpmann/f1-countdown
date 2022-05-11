<script>
    export let timestamp;
    export let timezoneOffset;
</script>

<style>
    h5 {
        margin-top: 100px;
        color: grey;
        text-align: center;
    }
</style>

<h5>{new Date(timestamp + timezoneOffset * 1000)}</h5>