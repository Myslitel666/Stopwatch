<script lang="ts">
  import { themeStore } from "svelte-elegant/stores";
  import { onMount, onDestroy } from "svelte";
  import { ButtonBox } from "svelte-elegant";

  let isInitialized = false;
  const timeStart = "00";
  let minutes = timeStart;
  let seconds = timeStart;
  let ms = timeStart;
  let timerInterval: number | null = null;

  function formatTimeUnits(value: number) {
    return (value % 60).toString().padStart(2, "0");
  }

  function initialTimer() {
    const startTime = Date.now();

    timerInterval = setInterval(() => {
      const elapsed = Date.now() - startTime;

      seconds = formatTimeUnits(Math.floor(elapsed / 1000));
      ms = formatTimeUnits(Math.floor(elapsed / 16.666));
    }, 10); // Интервал не важен, можно 16ms (~60fps)
  }

  function clearTimer() {
    if (timerInterval) {
      clearInterval(timerInterval);
      timerInterval = null;
    }
  }

  onMount(() => {
    isInitialized = true;
  });

  // ОЧИЩАЕМ ТАЙМЕР ПРИ УНИЧТОЖЕНИИ КОМПОНЕНТА
  onDestroy(() => {
    if (timerInterval) {
      clearInterval(timerInterval);
      timerInterval = null;
    }
  });
</script>

<div class="page">
  <div class="timer" style:color={$themeStore.palette.primary}>
    {minutes}:{seconds}<span class="ms">{ms}</span>
  </div>
  {#if timerInterval === null && minutes === timeStart && seconds === timeStart && ms === timeStart}
    <ButtonBox onClick={initialTimer} borderRadius="50%">S</ButtonBox>
  {:else}
    <div class="buttons">
      <ButtonBox onClick={clearTimer} borderRadius="50%">T</ButtonBox>
      <ButtonBox
        onClick={() => {
          minutes = timeStart;
          seconds = timeStart;
          ms = timeStart;
        }}
        borderRadius="50%">Y</ButtonBox
      >
    </div>
  {/if}
</div>

<style>
  .buttons {
    display: flex;
    gap: 10px;
  }

  .page {
    width: 100vw;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
  }

  .timer {
    font-size: 48px;
    margin-top: 30px;
    margin-bottom: 30px;
  }

  .ms {
    font-size: 20px;
    margin-left: 5px;
  }
</style>
