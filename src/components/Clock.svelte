<script>
  import { createEventDispatcher } from "svelte";
  import { fade } from "svelte/transition";
  export let date;
  const dispatch = createEventDispatcher();
  let hour = date.getHours() ? date.getHours() : 24;
  let minute = date.getMinutes();
  let seconds = date.getSeconds();
  let hourIndex = hour % 12;
  let size = hour > 12 ? -20 : -35;
  let selection = "hour";
  let minutes = createArray(60);
  function setHour(h) {
    hour = h;
    hourIndex = h % 12;
    size = h > 12 ? -20 : -35;
    date.setHours(h);
    setDate();
  }
  function setMinute(m) {
    minute = m;
    date.setMinutes(m);
    setDate();
  }
  function setSeconds(s) {
    seconds = s;
    date.setSeconds(s);
    setDate();
  }
  function createArray(number) {
    return Array.apply(null, Array(number)).map(function(x, i) {
      return i;
    });
  }
  function setDate() {
    dispatch("update", date);
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

  svg text {
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

  .hourCircle,
  .hit {
    stroke: transparent;
    fill: transparent;
    cursor: pointer;
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
  .line {
    stroke: #00acc1;
    stroke-width: 2;
  }
  g.mins text.title, text.title {
    fill: black;
    stroke: white;
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
      <text class="title" dominant-baseline="middle" text-anchor="middle" paint-order="stroke">
        Hour
      </text>
    </g>
  {:else if selection === 'minute'}
    <g transition:fade class="mins">
      {#each minutes as min, i}
        <g class={min === minute ? 'selected' : ''}>
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
      <text class="title" dominant-baseline="middle" text-anchor="middle" paint-order="stroke">
        Minute
      </text>
      {#each minutes as min, i}
        <path
          on:click={() => (selection = 'seconds')}
          on:mouseover={() => {
            setMinute(min);
          }}
          class="hit"
          d="M 0 0 L -2 -47 L 2 -47 Z"
          transform="rotate({6 * min})" />
      {/each}
    </g>
  {:else}
    <g transition:fade class="mins">

      {#each minutes as min, i}
        <g class={min === seconds ? 'selected' : ''}>
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
      <line class="line" y1="0" y2="-35" transform="rotate({6 * seconds})" />
      <text class="title" dominant-baseline="middle" text-anchor="middle" paint-order="stroke">
        Seconds
      </text>
      {#each minutes as min, i}
        <path
          on:click={() => dispatch('hide')}
          on:mouseover={() => {
            setSeconds(min);
          }}
          class="hit"
          d="M 0 0 L -2 -47 L 2 -47 Z"
          transform="rotate({6 * min})" />
      {/each}
    </g>
  {/if}
</svg>
