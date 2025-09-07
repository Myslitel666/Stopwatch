<script lang="ts">
  import { themeStore } from "svelte-elegant/stores";
  import { onMount, onDestroy } from "svelte";
  import { ButtonBox } from "svelte-elegant";
  import { Play, Pause } from "svelte-elegant/icons-elegant";

  let isInitialized = false;
  let isStopped = true;
  let elapsedTime = 0;
  let isReset = true;
  const timeStartConst = "00";
  let minutes = timeStartConst;
  let seconds = timeStartConst;
  let ms = timeStartConst;
  let timerInterval: number | null = null;

  function formatTimeUnits(value: number) {
    return (value % 60).toString().padStart(2, "0");
  }

  function initialTimer() {
    const elapsedTime = Date.now();

    timerInterval = setInterval(() => {
      const elapsed = Date.now() - elapsedTime;

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
  <div class="buttons">
    {#if isStopped}
      <ButtonBox
        onClick={() => {
          initialTimer();
          isStopped = false;
          isReset = false;
        }}
        borderRadius="50%"
      >
        <div
          style:display="flex"
          style:justify-content="center"
          style:width="100%"
          style:margin-left="5px"
        >
          <Play size="43px" />
        </div>
      </ButtonBox>
    {:else}
      <ButtonBox
        onClick={() => {
          clearTimer();
          isStopped = true;
        }}
        borderRadius="50%"
      >
        <div
          style:display="flex"
          style:justify-content="center"
          style:width="100%"
        >
          <Pause size="65px" />
        </div>
      </ButtonBox>
    {/if}
    {#if ms !== timeStartConst || seconds !== timeStartConst || minutes !== timeStartConst}
      <ButtonBox
        onClick={() => {
          clearTimer();
          minutes = timeStartConst;
          seconds = timeStartConst;
          ms = timeStartConst;
          isStopped = true;
          isReset = true;
        }}
        borderRadius="50%"
      >
        <div
          style:width="37px"
          style:height="37px"
          style:border-radius="6px"
          style:background-color={$themeStore.palette.text.main}
        ></div>
      </ButtonBox>
    {/if}
  </div>
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
