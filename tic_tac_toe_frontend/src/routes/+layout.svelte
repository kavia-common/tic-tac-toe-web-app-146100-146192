<script lang="ts">
  import '../app.css';
  let { children } = $props();
</script>

<main>
  <section class="stage">
    {@render children()}
  </section>
</main>

<style>
  main {
    min-height: 100vh;
    display: grid;
    place-items: center;
  }
  .stage {
    width: 100%;
    padding: 1.5rem 1rem;
  }
</style>
