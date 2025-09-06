<script lang="ts">
  import { themeStore, themeMode } from "svelte-elegant/stores";
  import { onMount, onDestroy } from "svelte";

  let isInitialized = false;
  const timeStart = "00";
  let minutes = timeStart;
  let seconds = timeStart;
  let ms = timeStart;
  let timerInterval: number | null = null;
  let theme;

  // Подписываемся на изменения темы
  themeStore.subscribe((value) => {
    theme = value; //Инициализация объекта темы
  });

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
    initialTimer();
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
</div>

<style>
  .page {
    width: 100vw;
    display: flex;
    justify-content: center;
  }

  .timer {
    font-size: 48px;
  }

  .ms {
    font-size: 20px;
    margin-left: 5px;
  }
</style>
