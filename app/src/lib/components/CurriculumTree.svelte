<script lang="ts">
    import { SvelteFlow, MiniMap, Controls, Background } from '@xyflow/svelte';
    import '@xyflow/svelte/dist/style.css';
    import { curriculumStore } from "$lib/states/curriculum.svelte";

    //$curriculumStore.courses.forEach(course => {console.log(course.id)})

    let nodes = $state.raw([]);
    let edges = $state.raw([]);

    $effect(() => {
        const nodesPerColumn = 10;
        const verticalSpacing = 100;
        const horizontalSpacing = 200;

        nodes = $curriculumStore.courses.map((course, index) => {
            const columnIndex = Math.floor(index / nodesPerColumn);
            const rowIndex = index % nodesPerColumn;

            return {
                id: `${course.id}-${index}`,
                position: {
                    x: columnIndex * horizontalSpacing,
                    y: rowIndex * verticalSpacing
                },
                data: { label: course.name }
            };
        });
    });
</script>

<div style:height="50rem">
  <SvelteFlow bind:nodes bind:edges>
    <MiniMap />
    <Controls />
    <Background />
  </SvelteFlow>
</div>

<style>

</style>
