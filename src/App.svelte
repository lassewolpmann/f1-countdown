<script>
	import { onMount } from 'svelte';

	export const eventNames = [
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
		"FORMULA 1 PIRELLI GRAN PREMIO D’ITALIA 2022",
		"FORMULA 1 SINGAPORE GRAND PRIX 2022",
		"FORMULA 1 JAPANESE GRAND PRIX 2022",
		"FORMULA 1 ARAMCO UNITED STATES GRAND PRIX 2022",
		"FORMULA 1 GRAN PREMIO DE LA CIUDAD DE MÉXICO 2022",
		"FORMULA 1 HEINEKEN GRANDE PRÊMIO DE SÃO PAULO 2022",
		"FORMULA 1 ETIHAD AIRWAYS ABU DHABI GRAND PRIX 2022"
	]

	let season = []

	let timezoneOffset = undefined

	let nextRace = undefined
	let nextRaceName = undefined
	
	const currentTimestamp = new Date().getTime()
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
					if (new Date(season[i].date).getTime() > currentTimestamp) {
						timezoneOffset = (new Date().getTimezoneOffset() * -1) * 60

						nextRace = season[i]
						nextRaceName = eventNames[i]
						nextRaceTimestamp = Date.parse(nextRace.date + ' ' + nextRace.time.slice(0, -1))

						deltaToRaceSeconds = Math.floor(((nextRaceTimestamp - currentTimestamp) / 1000)) + timezoneOffset

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

	
</script>

<style>
	h1 {
		text-align: center;
	}

	.countdown {
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

	.hard {
		box-shadow: 0px 0px 10px 10px white;
	}

	.medium {
		box-shadow: 0px 0px 10px 10px yellow;
	}

	.soft {
		box-shadow: 0px 0px 10px 10px red;
	}

	.wet {
		box-shadow: 0px 0px 10px 10px blue;
	}
</style>

<h1>{nextRaceName}</h1>
<div class='countdown'>
	<div class='countdown-time hard'>
		<span class='time'>{daysUntilNextRace}</span>
		<span>Days</span>
	</div>
	<div class='countdown-time medium'>
		<span class='time'>{hoursUntilNextRace}</span>
		<span>Hours</span>
	</div>
	<div class='countdown-time soft'>
		<span class='time'>{minutesUntilNextRace}</span>
		<span>Minutes</span>
	</div>
	<div class='countdown-time wet'>
		<span class='time'>{secondsUntilNextRace}</span>
		<span>Seconds</span>
	</div>
</div>
<h5>{new Date(nextRaceTimestamp + timezoneOffset * 1000)}</h5>