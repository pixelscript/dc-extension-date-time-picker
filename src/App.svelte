<script>
  import { init } from "dc-extensions-sdk";
  import { fly } from "svelte/transition";
  import Calendar from "./components/Calendar.svelte";
  import Clock from "./components/Clock.svelte";
  let date = new Date();
  let editingDate = false;
  let editingTime = false;
  let format = "date-time";
  let showDate = true;
  let showTime = true;
  let sdk;

  (async () => {
    try {
      sdk = await init();
      sdk.frame.startAutoResizer();
      setState(sdk.field.schema.format);
			const d = await sdk.field.getValue();
			console.log(d);
      if (d && format === "date-time") {
        date = new Date(d);
      } else if (d && format === "date") {
        date = new Date(d + 'T00:00:00');
      } else if (d && format === "time") {
        date = new Date('1983-08-16T' + d);
      }
    } catch (e) {}
  })();

  function setState(f) {
    if (!f) {
      return;
    }
    if (f == "time") {
      format = f;
      showDate = false;
    } else if (f == "date") {
      format = f;
      showTime = false;
    }
  }
  function update(v) {
    date = v;
    let str = date.toISOString();
    if (format !== "date-time") {
      let split = str.split("T");
      if (format === "date") {
        str = split[0];
      } else if (format === "time") {
        str = split[1];
      }
    }
    sdk.field.setValue(str);
  }

  function toggle(component) {
    if (component === "date") {
      if (editingDate) {
        editingDate = false;
      } else {
        editingDate = true;
        editingTime = false;
      }
    }
    if (component === "time") {
      if (editingTime) {
        editingTime = false;
      } else {
        editingTime = true;
        editingDate = false;
      }
    }
  }
</script>

<style>
  img {
    height: 2em;
  }

  p {
    text-decoration: underline;
    height: 2em;
    line-height: 2em;
    padding: 0;
    margin: 0;
  }

  img {
    padding-right: 0.25em;
  }

  img,
  p {
    float: left;
  }

  .date,
  .time,
  .label {
    display: inline-block;
    margin: 0.25em;
    cursor: pointer;
  }

  .label p {
    text-decoration: none;
    cursor: default;
  }
  .editor {
    float: left;
  }
  .clear {
    clear: both;
  }
</style>

<main>
  {#if sdk && sdk.field && sdk.field.schema && sdk.field.schema.title}
    <div class="label">
      <p>{sdk.field.schema.title}:</p>
    </div>
  {/if}
  {#if showDate}
    <div class="date" on:click={() => toggle('date')}>
      <img src="./icons/calendar.svg" alt="calendar icon" />
      <p>{date.toLocaleDateString()}</p>
    </div>
  {/if}
  {#if showTime}
    <div class="time" on:click={() => toggle('time')}>
      <img src="./icons/clock.svg" alt="calendar icon" />
      <p>{date.toLocaleTimeString()}</p>
    </div>
  {/if}
  <div class="clear" />
  {#if editingDate}
    <div class="editor" transition:fly={{ x: -500, duration: 500 }}>
      <Calendar
        {date}
        on:hide={() => (editingDate = false)}
        on:update={d => update(d.detail)} />
    </div>
  {/if}
  {#if editingTime}
    <div class="editor" transition:fly={{ x: -500, duration: 500 }}>
      <Clock
        {date}
        on:hide={() => (editingTime = false)}
        on:update={d => update(d.detail)} />
    </div>
  {/if}
  <div class="clear" />
</main>
