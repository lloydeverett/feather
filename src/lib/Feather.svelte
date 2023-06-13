<script>
  import { onMount, onDestroy } from 'svelte';
  import feather from '../assets/feather.png'
  import rectangle from '../assets/rectangle.png'

  let containerWidth;
  let containerHeight;
  let featherWidth;
  let featherHeight;
  let rectangleWidth;
  let rectangleHeight;

  const FEATHER_ACCELERATION = 0.001;
  const MAX_FEATHER_VELOCITY = 0.17;
  const RECTANGLE_VELOCITY_MAGNITUDE = 0.04;
  const FEATHER_CLICK_VELOCITY = -0.215;

  let featherVelocity = 0.01;
  let rectangleVelocity = +RECTANGLE_VELOCITY_MAGNITUDE;

  let mounted = true
  let featherRotateDeg = 0; // degrees
  let featherY = 0.0;
  let rectangleY = 0.0;
  let lastTime = Date.now();
  let totalElapsed = 0.0

  function onClick() {
    featherVelocity = FEATHER_CLICK_VELOCITY;
  }

  function onFrame() {
    if (!mounted) {
      return
    }

    const now = Date.now();
    const elapsed = now - lastTime;
    lastTime = now;
    if (elapsed > 100) {
        // skip the frame
        requestAnimationFrame(onFrame)
        return
    }
    totalElapsed += elapsed

    // update feather velocity
    featherVelocity += FEATHER_ACCELERATION * elapsed;
    featherVelocity = Math.min(featherVelocity, MAX_FEATHER_VELOCITY);

    // update rectangle velocity
    const RECTANGLE_MARGIN_PX = 20;
    if (rectangleY + (rectangleHeight / 2) > (containerHeight / 2) - RECTANGLE_MARGIN_PX) {
        rectangleVelocity = -RECTANGLE_VELOCITY_MAGNITUDE;
    } else if (rectangleY - (rectangleHeight / 2) < -(containerHeight / 2) + RECTANGLE_MARGIN_PX) {
        rectangleVelocity = +RECTANGLE_VELOCITY_MAGNITUDE;
    }

    // update positions
    featherY += featherVelocity * elapsed;
    rectangleY += rectangleVelocity * elapsed;
    featherY = Math.min(featherY, (featherHeight / 2) + (containerHeight / 2))

    // update feather rotation
    featherRotateDeg = Math.sin(totalElapsed * 0.002) * 8;

    requestAnimationFrame(onFrame)
  }

  onMount(() => {
    requestAnimationFrame(onFrame)
  })
  onDestroy(() => {
    mounted = false
  })
</script>

<div class="container" bind:clientWidth={containerWidth} bind:clientHeight={containerHeight} on:click={onClick} on:keydown={onClick}>
  <div class="feather" bind:clientWidth={featherWidth} bind:clientHeight={featherHeight} style="transform: translate(-50%, calc(-50% + {featherY}px)) rotate({featherRotateDeg}deg);">
    <img src={feather} alt="Feather">
  </div>
  <div class="rectangle" bind:clientWidth={rectangleWidth} bind:clientHeight={rectangleHeight} style="transform: translate(-50%, calc(-50% + {rectangleY}px));">
    <img src={rectangle} alt="Rectangle">
  </div>
</div>

<style>
  .container {
    overflow: hidden;
    user-select: none;
    position: relative;
    background: black;
    height: 60vh;
    border: 1px solid #bbb;
    width: 100%;
  }

  .feather, .rectangle {
    position: absolute;
    left: 50%;
    top: 50%;
  }
</style>
