<script>
  export let showForm;
</script>

<nav class="nav">
  <div class="nav-center">
    <h1 class="nav-title">Budget Calculator</h1>
    <button type="button" class="nav-btn" on:click={showForm}>
      <i class="far fa-plus-square" />
      add item
    </button>
  </div>
</nav>
