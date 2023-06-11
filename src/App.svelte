<script lang="ts">
  import Timer from './lib/Timer.svelte'
  import Feather from './lib/Feather.svelte'
</script>

<main>
  <input type="text" placeholder="What I want to focus on...">

  <Feather />

  <Timer />
</main>

<style>
  body {
    margin: none;
  }

  main {
    height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-evenly;
  }

  input {
    font-size: 24px;
    width: 400px;
    padding: 6px;
  }
</style>
