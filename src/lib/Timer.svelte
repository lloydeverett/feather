<script>
  import { onDestroy } from 'svelte';
 
  let started = false;
  let durationStr;
  let timeLeft;
  let intervalId;
  
  let completedTimers = 0;
  const minDots = 3;
 
  function parseSeconds(str) {
        if (typeof str !== 'string') {
            return NaN
        }
        let totalSeconds = 0
        const days = str.match(/(\d+)\s*d/)
        const hours = str.match(/(\d+)\s*h/)
        const minutes = str.match(/(\d+)\s*m/)
        const seconds = str.match(/(\d+)\s*s/)
        if (!days && !hours && !minutes && !seconds) {
            return NaN
        }
        if (days) { totalSeconds += parseInt(days[1])*86400 }
        if (hours) { totalSeconds += parseInt(hours[1])*3600 }
        if (minutes) { totalSeconds += parseInt(minutes[1])*60 }
        if (seconds) { totalSeconds += parseInt(seconds[1]) }
        return totalSeconds
  }
 
  function startTimer() {
    if (started) {
        return;
    }
    timeLeft = parseSeconds(durationStr);
    if (isNaN(timeLeft)) {
        return;
    }
    started = true;
    intervalId = setInterval(() => {
      timeLeft--;
      if (timeLeft === 0) {
        started = false
        completedTimers++;
        clearInterval(intervalId);
      }
    }, 1000);
  }
 
  function stopTimer() {
    started = false;
    clearInterval(intervalId);
  }
 
  onDestroy(() => {
    stopTimer();
  });
</script>
 
<div>
  <div class="timer-stuff">
    <input style="display: {started ? 'none' : 'block'}; border: none; {(isNaN(parseSeconds(durationStr)) && durationStr) ? 'border-bottom: 1px solid red;' : ''}" id="duration" bind:value={durationStr} />
    <span style="display: {started ? 'block' : 'none'};">{timeLeft} seconds</span>
    <button on:click={startTimer} style="display: {started ? 'none' : 'block'};">Start</button>
    <button on:click={stopTimer} style="display: {started ? 'block' : 'none'};">Stop</button>
  </div>
  <div>
    {#each [...Array(Math.max(completedTimers, minDots)).keys() /* range: 1,2,...,max(completedTimers, minDots) */] as index}
      <span class="dot {index < completedTimers ? 'dot-filled' : ''}"></span>
    {/each}
  </div>
 </div>
 
<style>
  input {
    width: 60px;
  }

  .timer-stuff {
    font-size: 24px;
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    margin-bottom: 20px;
  }
 
  span {
    display: block;
    margin-right: 10px;
  }
 
  input {
    padding: 5px;
    font-size: 14px;
    width: 100px;
    margin-right: 10px;
  }
 
  button {
    padding: 8px 16px;
    font-size: 14px;
  }

  .dot {
    height: 14px;
    width: 14px;
    border-radius: 50%;
    border: 2px solid #bbb;
    display: inline-block;
  }

  .dot-filled {
    background: #bbb;
  }
</style>