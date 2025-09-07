<script>
  import ColorThemeSwitch from "svelte-elegant/ColorThemeSwitch";
  import { goto } from "$app/navigation";
  import Header from "svelte-elegant/Header";
  import { Stopwatch } from "svelte-elegant/icons-elegant";

  import { themeMode, themeStore } from "svelte-elegant/stores";

  let theme;

  let logotypeFilter = "";
  let svelteColor = "";
  let elegantColor = "";

  // Подписываемся на изменения темы
  themeStore.subscribe((value) => {
    theme = value; //Инициализация объекта темы

    svelteColor = theme.palette.primary;
    elegantColor = $themeMode === "light" ? "#383838" : "#fdfdfd";
    logotypeFilter = $themeMode === "light" ? "brightness(80%)" : "";
  });
</script>

<Header>
  <button
    style:margin-left="-2px"
    style:gap="1px"
    onclick={() => goto("/settings")}
  >
    <Stopwatch size="38px" />
    <p style:font-size="26px" style:margin-top="4px">
      <span
        style:color={svelteColor}
        style:filter={logotypeFilter}
        style:transition="all 0.3s"
      >
        Stop
      </span>
      <span
        style:color={elegantColor}
        style:transition="all 0.3s"
        style:margin-left="-5px"
      >
        watch
      </span>
    </p>
  </button>
  <div style:margin-left="auto" style:margin-right="1px">
    <ColorThemeSwitch />
  </div>
</Header>

<style>
  button {
    margin-left: 6px;
  }
  @media (max-width: 768px) {
    button {
      margin-left: 3px;
    }
  }
</style>
