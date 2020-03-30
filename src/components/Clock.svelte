<script>
  import { onMount } from "svelte";
  import { fade } from "svelte/transition";
  export let date;
  let hour = date.getHours();
  let minute = date.getMinutes();
  let second = date.getSeconds();
  let hourIndex = hour % 12;
  let size = hour > 12 ? -20 : -35;
  let selection = "hour";
  let minutes = createArray(60);
  function setHour(h) {
    hour = h;
    hourIndex = h % 12;
    size = h > 12 ? -20 : -35;
  }
  function setMinute(m) {
    minute = m;
  }
  function createArray(number) {
    return Array.apply(null, Array(number)).map(function(x, i) {
      return i;
    });
  }
</script>

<style>
  .clock {
    width: 100%;
    height: 100%;
    max-width: 20em;
  }

  circle {
    stroke-width: 0.1;
  }

  svg text,
  svg rect {
    transform-origin: 50% 50%;
  }

  .label {
    font-size: 0.5em;
  }
  .label-inner {
    font-size: 0.25em;
  }

  .clock-face {
    stroke: #555;
    fill: white;
  }

  .hourCircle {
    stroke: transparent;
    fill: transparent;
  }
  g.mins text {
    fill: transparent;
  }
  g.mins text.show {
    fill: black;
  }
  g.selected .hourCircle {
    fill: #00acc1;
  }

  g.selected text,
  g.selected text.show {
    fill: white;
  }
  g .hourCircle,
  g text {
    cursor: pointer;
  }

  .minor {
    stroke: #999;
    stroke-width: 0.5;
  }

  .major {
    stroke: #555;
    stroke-width: 1;
  }

  .line {
    stroke: #00acc1;
    stroke-width: 2;
  }

  .minute {
    stroke: #666;
  }

  .second,
  .second-counterweight {
    stroke: rgb(180, 0, 0);
  }

  .second-counterweight {
    stroke-width: 1;
  }
</style>

<svg class="clock" viewBox="-50 -50 100 100">
  <circle class="clock-face" r="48" />
  {#if selection === 'hour'}
    <g transition:fade>
      {#each [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12] as minute, i}
        <g
          on:click={() => (selection = 'minute')}
          on:mouseover={() => {
            setHour(minute);
          }}
          class={minute === hour ? 'selected' : ''}>
          <circle
            class="hourCircle"
            cx="0"
            cy="-40"
            r="6"
            transform="rotate({30 * minute})" />
          <g transform="rotate({30 * minute})">
            <svg x="-10" y="-45" width="20" height="10">
              <text
                transform="rotate(-{30 * minute}) translate(0,1)"
                class="label"
                x="50%"
                y="50%"
                dominant-baseline="middle"
                text-anchor="middle">
                {minute}
              </text>
            </svg>
          </g>
        </g>
        <g
          on:click={() => (selection = 'minute')}
          on:mouseover={() => {
            setHour(minute + 12);
          }}
          class={minute + 12 === hour ? 'selected' : ''}>
          <circle
            class="hourCircle"
            cx="0"
            cy="-25"
            r="6"
            transform="rotate({30 * minute})" />
          <g transform="rotate({30 * minute})">
            <svg x="-10" y="-30" width="20" height="10">
              <text
                transform="rotate(-{30 * minute}) translate(0,1)"
                class="label-inner"
                x="50%"
                y="50%"
                dominant-baseline="middle"
                text-anchor="middle">
                {minute + 12}
              </text>
            </svg>
          </g>
        </g>
      {/each}
      <line
        class="line"
        y1="0"
        y2={size}
        transform="rotate({30 * hourIndex})" />
    </g>
  {:else}
    <g transition:fade class="mins">
      {#each minutes as min, i}
        <g
          on:click={() => (selection = 'minute')}
          on:mouseover={() => {
            setMinute(min);
          }}
          class={min === minute ? 'selected' : ''}>
          <circle
            class="hourCircle"
            cx="0"
            cy="-40"
            r="6"
            transform="rotate({6 * min})" />
          <g transform="rotate({6 * min})">
            <svg x="-10" y="-45" width="20" height="10">
              <text
                class={min % 5 === 0 ? 'label show' : 'label'}
                transform="rotate(-{6 * min}) translate(0,1)"
                x="50%"
                y="50%"
                dominant-baseline="middle"
                text-anchor="middle">
                {min}
              </text>
            </svg>
          </g>
        </g>
      {/each}
      <line class="line" y1="0" y2="-35" transform="rotate({6 * minute})" />
    </g>
  {/if}

  <!-- minute hand
  <line
    class="minute"
    y1="4"
    y2="-30"
    transform="rotate({6 * minutes + seconds / 10})" />

  <!-- second hand
  <g transform="rotate({6 * seconds})">
    <line class="second" y1="10" y2="-38" />
    <line class="second-counterweight" y1="10" y2="2" />
  </g> -->
</svg>
<h1>{hour}</h1>
<h1>{size}</h1>
