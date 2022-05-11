<script>
	import { onMount } from 'svelte';

	let current = 'Race'

	const eventNames = [
		"FORMULA 1 GULF AIR BAHRAIN GRAND PRIX 2022",
		"FORMULA 1 STC SAUDI ARABIAN GRAND PRIX 2022",
		"FORMULA 1 HEINEKEN AUSTRALIAN GRAND PRIX 2022",
		"FORMULA 1 ROLEX GRAN PREMIO DEL MADE IN ITALY E DELL'EMILIA-ROMAGNA 2022",
		"FORMULA 1 CRYPTO.COM MIAMI GRAND PRIX 2022",
		"FORMULA 1 PIRELLI GRAN PREMIO DE ESPAÑA 2022",
		"FORMULA 1 GRAND PRIX DE MONACO 2022",
		"FORMULA 1 AZERBAIJAN GRAND PRIX 2022",
		"FORMULA 1 GRAND PRIX DU CANADA 2022",
		"FORMULA 1 LENOVO BRITISH GRAND PRIX 2022",
		"FORMULA 1 GROSSER PREIS VON ÖSTERREICH 2022",
		"FORMULA 1 LENOVO GRAND PRIX DE FRANCE 2022",
		"FORMULA 1 ARAMCO MAGYAR NAGYDÍJ 2022",
		"FORMULA 1 ROLEX BELGIAN GRAND PRIX 2022",
		"FORMULA 1 HEINEKEN DUTCH GRAND PRIX 2022",
		"FORMULA 1 PIRELLI GRAN PREMIO D'ITALIA 2022",
		"FORMULA 1 SINGAPORE GRAND PRIX 2022",
		"FORMULA 1 JAPANESE GRAND PRIX 2022",
		"FORMULA 1 ARAMCO UNITED STATES GRAND PRIX 2022",
		"FORMULA 1 GRAN PREMIO DE LA CIUDAD DE MÉXICO 2022",
		"FORMULA 1 HEINEKEN GRANDE PRÊMIO DE SÃO PAULO 2022",
		"FORMULA 1 ETIHAD AIRWAYS ABU DHABI GRAND PRIX 2022"
	]

	let season = []

	let timezoneOffset = undefined

	let nextEvent = undefined
	let nextEventName = undefined
	let nextEventTrack = undefined
	let nextEventSessions = []
	
	const mountTimestamp = new Date().getTime()
	let nextRaceTimestamp = undefined
	let deltaToRaceSeconds = undefined

	let daysUntilNextRace = undefined
	let hoursUntilNextRace = undefined
	let minutesUntilNextRace = undefined
	let secondsUntilNextRace = undefined

	onMount(() => {
		fetch('https://ergast.com/api/f1/' + new Date().getFullYear() + '.json')
			.then(response => response.json())
			.then(result => season = result.MRData.RaceTable.Races)
			.then(season => {
				for (let i = 0; i < season.length; i++) {
					if (new Date(season[i].date).getTime() > mountTimestamp) {
						timezoneOffset = (new Date().getTimezoneOffset() * -1) * 60

						nextEvent = season[i]
						nextEventTrack = nextEvent.Circuit.circuitName
						nextEventSessions = Object.keys(nextEvent).slice(-4)
						nextEventName = eventNames[i]

						nextRaceTimestamp = Date.parse(nextEvent.date + ' ' + nextEvent.time.slice(0, -1))

						deltaToRaceSeconds = ((nextRaceTimestamp - mountTimestamp) / 1000) + timezoneOffset

						daysUntilNextRace = Math.floor(deltaToRaceSeconds / 86400)
						hoursUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 / 3600)
						minutesUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 / 60)
						secondsUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 % 60)

						break
					}
				}

				setInterval(() => {
					deltaToRaceSeconds -= 1
					
					daysUntilNextRace = Math.floor(deltaToRaceSeconds / 86400)
					hoursUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 / 3600)
					minutesUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 / 60)
					secondsUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 % 60)
				}, 1000)
			})
	});

	function calculateDeltaToSession(session) {
		let nextSessionDate = undefined
		let nextSessionTime = undefined

		if (session === 'Race') {
			nextSessionDate = nextEvent.date
			nextSessionTime = nextEvent.time
		} else {
			nextSessionDate = nextEvent[session].date
			nextSessionTime = nextEvent[session].time
		}

		nextRaceTimestamp = Date.parse(nextSessionDate + ' ' + nextSessionTime.slice(0, -1))
		deltaToRaceSeconds = ((nextRaceTimestamp - new Date().getTime()) / 1000) + timezoneOffset

		daysUntilNextRace = Math.floor(deltaToRaceSeconds / 86400)
		hoursUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 / 3600)
		minutesUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 / 60)
		secondsUntilNextRace = Math.floor(deltaToRaceSeconds % 86400 % 3600 % 60)
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
		margin: 20px 20px 0px 20px;
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
		max-width: 200px;
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