<script lang="ts">
  import "bootstrap/dist/css/bootstrap.css";
  import scriptSrc from "bootstrap/dist/js/bootstrap.bundle.js?url";
  interface Props {
    children?: import("svelte").Snippet;
  }

  let { children }: Props = $props();
  let theme = $state("light");

  const onThemeChange = async () => {
    theme = theme == "light" ? "dark" : "light";
    localStorage.setItem("theme", theme);
  };

  $effect(() => {
    theme = localStorage.getItem("theme") || "light";
    document
      .getElementsByTagName("body")[0]
      .setAttribute("data-bs-theme", theme);
  });
</script>

<svelte:head>
  <script src={scriptSrc}></script>
</svelte:head>

<div class="font-monospace vh-100 d-flex flex-column">
  <nav class="p-1 d-flex navbar">
    <h1 class="header ms-3">Catculator!</h1>
    <div>
      <div class="form-check form-switch me-5">
        <input
          class="form-check-input mt-auto me-3"
          type="checkbox"
          role="switch"
          id="dark"
          style="height: 1.5em; width: 3em;"
          onclick={onThemeChange}
          checked={theme == "dark" ? true : false}
        />
        <label class="form-check-label" for="dark">Dark</label>
      </div>
    </div>
  </nav>
  {@render children?.()}
</div>

<style>
  :root {
    --bs-primary: #bb1c8e;
    --bs-primary-rgb: 145, 11, 107;
  }

  .header {
    color: #bb1c8e;
  }
</style>
