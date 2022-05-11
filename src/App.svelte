<script>
	import { onMount } from 'svelte';

	let current = 'Race';

	let season = [];

	let timezoneOffset;

	let nextEvent = undefined
	let nextEventName = undefined
	let nextEventTrack = undefined
	let nextEventSessions = []
	
	const mountTimestamp = new Date().getTime();

	let nextRaceTimestamp, deltaToRaceSeconds;

	let daysUntilNextRace, hoursUntilNextRace, minutesUntilNextRace, secondsUntilNextRace;


	onMount(() => {
		fetch('https://ergast.com/api/f1/' + new Date().getFullYear() + '.json')
			.then(response => response.json())
			.then(result => {
				season = result['MRData']['RaceTable']['Races'];
				season = season.filter((item) => new Date(item.date).getTime() > mountTimestamp);

				if (season.length > 0) {
					timezoneOffset = (new Date().getTimezoneOffset() * -1) * 60;

					nextEvent = season[0];
					nextEventTrack = nextEvent['Circuit']['circuitName'];
					nextEventSessions = Object.keys(nextEvent).slice(-4);
					nextEventName = nextEvent['raceName'];

					nextRaceTimestamp = Date.parse(nextEvent.date + ' ' + nextEvent.time.slice(0, -1));

					deltaToRaceSeconds = ((nextRaceTimestamp - mountTimestamp) / 1000) + timezoneOffset;

					daysUntilNextRace = Math.floor(deltaToRaceSeconds / 86400);
					hoursUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 / 3600);
					minutesUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 / 60);
					secondsUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 % 60);

					setInterval(() => {
						deltaToRaceSeconds -= 1

						daysUntilNextRace = Math.floor(deltaToRaceSeconds / 86400);
						hoursUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 / 3600);
						minutesUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 / 60);
						secondsUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 % 60);
					}, 1000);

				} else {
					console.log('no races left');
				}
			});
	});

	function calculateDeltaToSession(session) {
		let nextSessionDate = session === 'Race' ? nextEvent.date : nextEvent[session].date;
		let nextSessionTime = session === 'Race' ? nextEvent.time : nextEvent[session].time;

		nextRaceTimestamp = Date.parse(nextSessionDate + ' ' + nextSessionTime.slice(0, -1));
		deltaToRaceSeconds = ((nextRaceTimestamp - new Date().getTime()) / 1000) + timezoneOffset;

		daysUntilNextRace = Math.floor(deltaToRaceSeconds / 86400);
		hoursUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 / 3600);
		minutesUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 / 60);
		secondsUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 % 60);
	}
</script>

<style>
	h1, h4, h5 {
		text-align: center;
	}

	h4 {
		color: grey;
		margin-top: -40px;
	}

	h5 {
		margin-top: 100px;
	}

	button {
		background: none;
		color: white;
		font-family: inherit;
		cursor: pointer;
		font-weight: bold;
		font-size: 1.25rem;
		margin: 20px 20px 0 20px;
		border: none;
	}

	.row {
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		align-items: center;
		justify-content: center;
	}

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

	.time-text {
		color: grey;
	}

	.hard {
		border: 10px solid white;
	}

	.medium {
		border: 10px solid yellow;
	}

	.soft {
		border: 10px solid red;
	}

	.wet {
		border: 10px solid blue;
	}

	.selected {
		color: red;
	}
</style>

<h1>{nextEventName}</h1>
<h4>{nextEventTrack}</h4>
<div class='row'>
	<div class='countdown-time hard'>
		<span class='time'>{daysUntilNextRace}</span>
		<span class='time-text'>Days</span>
	</div>
	<div class='countdown-time medium'>
		<span class='time'>{hoursUntilNextRace}</span>
		<span class='time-text'>Hours</span>
	</div>
	<div class='countdown-time soft'>
		<span class='time'>{minutesUntilNextRace}</span>
		<span class='time-text'>Minutes</span>
	</div>
	<div class='countdown-time wet'>
		<span class='time'>{secondsUntilNextRace}</span>
		<span class='time-text'>Seconds</span>
	</div>
</div>
<div class='row'>
	{#each nextEventSessions as session}
		{#if session === 'FirstPractice'}
			<button 
				class="{current === session ? 'selected' : ''}" 
				on:click="{() => current = session}"
				on:click="{() => calculateDeltaToSession(session)}">FP1
			</button>
		{:else if session === 'SecondPractice'}
			<button 
				class="{current === session ? 'selected' : ''}" 
				on:click="{() => current = session}"
				on:click="{() => calculateDeltaToSession(session)}">FP2
			</button>
		{:else if session === 'ThirdPractice'}
			<button 
				class="{current === session ? 'selected' : ''}" 
				on:click="{() => current = session}"
				on:click="{() => calculateDeltaToSession(session)}">FP3
			</button>
		{:else}
			<button 
				class="{current === session ? 'selected' : ''}" 
				on:click="{() => current = session}"
				on:click="{() => calculateDeltaToSession(session)}">{session}
			</button>
		{/if}
	{/each}
	<button 
		class="{current === 'Race' ? 'selected' : ''}" 
		on:click="{() => current = 'Race'}"
		on:click="{() => calculateDeltaToSession('Race')}">Race
	</button>
</div>
<h5>{new Date(nextRaceTimestamp + timezoneOffset * 1000)}</h5>