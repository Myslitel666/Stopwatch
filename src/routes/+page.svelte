<script lang="ts">
  import { themeStore } from "svelte-elegant/stores";
  import { onMount, onDestroy } from "svelte";
  import { ButtonBox } from "svelte-elegant";
  import { Play, Pause } from "svelte-elegant/icons-elegant";

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
    <ButtonBox onClick={initialTimer} borderRadius="50%">
      <div
        style:display="flex"
        style:justify-content="center"
        style:width="100%"
        style:margin-left="5px"
      >
        <Play size="45px" />
      </div>
    </ButtonBox>
  {:else}
    <div class="buttons">
      <ButtonBox onClick={clearTimer} borderRadius="50%">
        <div
          style:display="flex"
          style:justify-content="center"
          style:width="100%"
        >
          <Pause size="60px" />
        </div>
      </ButtonBox>
      <ButtonBox
        onClick={() => {
          minutes = timeStart;
          seconds = timeStart;
          ms = timeStart;
        }}
        borderRadius="50%"
      >
        <div
          style:width="35px"
          style:height="35px"
          style:border-radius="6px"
          style:background-color={$themeStore.palette.text.main}
        ></div>
      </ButtonBox>
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
