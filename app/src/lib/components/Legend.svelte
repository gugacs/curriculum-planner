<script>
  const COLOR_DEFAULT = "lightgrey";
  const COLOR_WORK = "#B7EBE8";
  const COLOR_DONE = "#CEEBB7";
  const COLOR_LAST_DEGREE = "#F6C892";
  const COLOR_INVALID = "#F69292";
</script>

<div class="legend-wrapper">
  <h1>Legend</h1>
  <div class="legend-items">
    <h2 style="--legend-color: {COLOR_DEFAULT}">default</h2>
    <h2 style="--legend-color: {COLOR_WORK}">in work</h2>
    <h2 style="--legend-color: {COLOR_DONE}">done</h2>
    <h2 style="--legend-color: {COLOR_LAST_DEGREE}">has prerequisite in last degree</h2>
    <h2 style="--legend-color: {COLOR_INVALID}">invalid course order</h2>
  </div>
</div>

<style>
  .legend-wrapper {
    display: flex;
    align-self: center; /* TODO maybe change this after the view exists*/
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 27rem;
    border-radius: 1rem;
    border: 3px solid lightgrey;
    padding: 0.1rem;

    h1 {
      font-size: 0.8rem;
      padding: 0;
      margin: 0;
    }

    .legend-items {
      display: flex;
      flex-direction: row;
      gap: 0.4rem;

      h2 {
        font-size: 0.65rem;
        margin: 0;
        padding-left: 0.3rem;
        padding-right: 0.3rem;
        border-radius: 0.75rem;
        background-color: var(--legend-color);
      }
    }
  }

</style>