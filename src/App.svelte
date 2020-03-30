<script>
	import { init } from 'dc-extensions-sdk';
	import { fly } from 'svelte/transition';
	import Calendar from './components/Calendar.svelte';
	import Clock from './components/Clock.svelte';
	let date = new Date();
	let editingDate = false;
	let editingTime = true;
	let sdk;
	(async () => {
		try {
			sdk = await init();
			sdk.frame.startAutoResizer();
		} catch(e) {

		}
	})();

	function toggle(component){
		if(component === 'date') {
			if(editingDate){
				editingDate = false;
			} else {
				editingDate = true;
				editingTime = false;
			}
		}
		if(component === 'time') {
			if(editingTime){
				editingTime = false;
			} else {
				editingTime = true;
				editingDate = false;
			}
		}
	}
</script>

<main>
	{#if sdk && sdk.field && sdk.field.schema && sdk.field.schema.title}
		<div class="label"><p>{sdk.field.schema.title}:</p></div>
	{/if}
	<div class="date" on:click={()=>toggle('date')}>
		<img src="./icons/calendar.svg" alt="calendar icon"/>
		<p>{date.toLocaleDateString()}</p>
	</div>
	<div class="time" on:click={()=>toggle('time')}>
		<img src="./icons/clock.svg" alt="calendar icon"/>
		<p>{date.toLocaleTimeString()}</p>
	</div>
	<div class="clear"></div>
	{#if editingDate}
		<div class="editor" transition:fly="{{ x: -500, duration: 500 }}">
			<Calendar {date} on:hide={()=>editingDate=false} on:update="{(d) => date = d.detail}"></Calendar>
		</div>
	{/if}
	{#if editingTime}
		<div class="editor" transition:fly="{{ x: -500, duration: 500 }}">
			<Clock {date} on:hide={()=>editingTime=false} on:update="{(d) => date = d.detail}"></Clock>
		</div>
	{/if}
	<div class="clear"></div>
</main>

<style>
	img {
		height: 2em;
	}

	P {
		text-decoration: underline;
		height: 2em;
		line-height: 2em;
		padding: 0;
		margin: 0;
	}

	img {
		padding-right: 0.25em;
	}

	img, p {
		float: left;
	}

	.date, .time, .label {
		display: inline-block;
		margin:0.25em;
		cursor:pointer;
	}

	.label p {
		text-decoration: none;
		cursor: default;
	}
	.editor {
		float:left;
	}
	.clear {
		clear:both;
	}
</style>