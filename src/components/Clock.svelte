<script>
  import { onMount } from "svelte";

  let time = new Date();

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

  svg text{
    transform-origin: 50% 50%;
  }

  .label {
    font-size: 0.5em;
  }

  .clock-face {
    stroke: #555;
    fill: white;
  }

  .hourHit {
    stroke: transparent;
    fill: #eee;
  }

  .minor {
    stroke: #999;
    stroke-width: 0.5;
  }

  .major {
    stroke: #555;
    stroke-width: 1;
  }

  .hour {
    stroke: #333;
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

  <!-- markers -->
  <!-- {#each [0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55] as minute, i}
    <line class="major" y1="35" y2="45" transform="rotate({30 * minute})" />
    {#each [1, 2, 3, 4] as offset}
      <line
        class="minor"
        y1="42"
        y2="45"
        transform="rotate({6 * (minute + offset)})" />
    {/each}
  {/each} -->

  <!-- hitarea -->
  {#each [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12] as minute, i}
    <circle
      class="hourHit"
      cx="0"
      cy="-40"
      r="4"
      transform="rotate({30 * minute})" />
    <g transform="rotate({30 * minute})">
      <svg x="-9.4" y="-44.6" width="20" height="10">
        <text
          transform="rotate(-{30 * minute})"
          class="label"
          x="50%"
          y="50%"
          dominant-baseline="middle"
          text-anchor="middle">
          {minute}
        </text>
      </svg>
    </g>

    <circle
      class="hourHit"
      cx="0"
      cy="-25"
      r="4"
      transform="rotate({30 * minute})" />
    <g transform="rotate({30 * minute})">
      <svg x="-4.5" y="-30" width="10" height="10">
        <text
          transform="rotate(-{30 * minute})"
          class="label"
          x="50%"
          y="50%"
          dominant-baseline="middle"
          text-anchor="middle">
          {minute + 12}
        </text>
      </svg>
    </g>
  {/each}

  <!-- hour hand
  <line
    class="hour"
    y1="2"
    y2="-20"
    transform="rotate({30 * hours + minutes / 2})" />

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
