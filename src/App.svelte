<script>
	import { onMount } from 'svelte';

	import Timer from './components/Timer.svelte';
	import Button from './components/Button.svelte';

	let current = 'Race';
	const d = new Date();
	const mountTimestamp = d.getTime();

	let season = [];
	let seasonFiltered = [];

	let timezoneOffset;

	let nextEvent, nextEventName, nextEventTrack;
	let nextEventSessions = [];

	let nextSessionTimestamp, deltaToSessionSeconds;

	let daysUntilNextSession, hoursUntilNextSession, minutesUntilNextSession, secondsUntilNextSession;

	onMount(() => {
		fetch('https://ergast.com/api/f1/' + d.getFullYear() + '.json')
			.then(response => response.json())
			.then(result => {
				season = result['MRData']['RaceTable']['Races'];
				seasonFiltered = season.filter((item) => new Date(item['date']).getTime() > mountTimestamp);

				timezoneOffset = (d.getTimezoneOffset() * -1) * 60;

				if (seasonFiltered.length > 0) {
					nextEvent = seasonFiltered[0];

				} else if (seasonFiltered.length === 0 && season.length > 0) {
					nextEvent = season[season.length -1]

				} else {
					console.log('Something terrible happened.')
					// this shouldn't happen
				}

				nextEventTrack = nextEvent['Circuit']['circuitName'];
				nextEventSessions = Object.keys(nextEvent).slice(-4);
				nextEventName = nextEvent['raceName'];

				calculateDelta('Race');

				setInterval(() => {
					deltaToSessionSeconds -= 1

					daysUntilNextSession = Math.floor(deltaToSessionSeconds / 86400);
					hoursUntilNextSession = Math.floor(deltaToSessionSeconds % 86400 / 3600);
					minutesUntilNextSession = Math.floor(deltaToSessionSeconds % 86400 % 3600 / 60);
					secondsUntilNextSession = Math.floor(deltaToSessionSeconds % 86400 % 3600 % 60);
				}, 1000);
			});
	});

	function calculateDelta(session) {
		const d = new Date();

		let nextSessionDate = session === 'Race' ? nextEvent['date'] : nextEvent[session]['date'];
		let nextSessionTime = session === 'Race' ? nextEvent['time'] : nextEvent[session]['time'];

		nextSessionTimestamp = Date.parse(nextSessionDate + ' ' + nextSessionTime.slice(0, -1));
		deltaToSessionSeconds = ((nextSessionTimestamp - d.getTime()) / 1000) + timezoneOffset;

		daysUntilNextSession = Math.floor(deltaToSessionSeconds / 86400);
		hoursUntilNextSession = Math.floor(deltaToSessionSeconds % 86400 / 3600);
		minutesUntilNextSession = Math.floor(deltaToSessionSeconds % 86400 % 3600 / 60);
		secondsUntilNextSession = Math.floor(deltaToSessionSeconds % 86400 % 3600 % 60);
	}
</script>

<style>
	h1, h4, h5 {
		text-align: center;
	}

	div {
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		align-items: center;
		justify-content: center;
	}
</style>

<h1>{nextEventName}</h1>
<h4 style="margin-top: -30px; color: grey">{nextEventTrack}</h4>
<div>
	<Timer time={daysUntilNextSession} text={'days'}/>
	<Timer time={hoursUntilNextSession} text={'hours'}/>
	<Timer time={minutesUntilNextSession} text={'minutes'}/>
	<Timer time={secondsUntilNextSession} text={'seconds'}/>
</div>
<div>
	{#each nextEventSessions as session}
		<Button bind:current={current} session={session} calculateDelta={() => calculateDelta(session)} />
	{/each}
	<Button bind:current={current} session={'Race'} calculateDelta={() => calculateDelta('Race')}/>
</div>
<h5 style="margin-top: 100px; color: grey">{new Date(nextSessionTimestamp + timezoneOffset * 1000)}</h5>