<script lang="ts">
    import { SvelteFlow, MiniMap, Controls, Background, Handle } from '@xyflow/svelte';
    import '@xyflow/svelte/dist/style.css';
    import { curriculumStore } from "$lib/states/curriculum.svelte";
    import CustomNode from "$lib/components/CustomNode.svelte";

    const nodeTypes = { custom: CustomNode };

    let nodes = $state.raw([]);
    let edges = $state.raw([]);

    $effect(() => {
        const nodesPerColumn = 10;
        const verticalSpacing = 200;
        const horizontalSpacing = 350;

        nodes = $curriculumStore.courses.map((course, index) => {
            const columnIndex = Math.floor(index / nodesPerColumn);
            const rowIndex = index % nodesPerColumn;

            return {
                id: `${course.id}-${index}`,
                type: 'custom',
                position: {
                    x: columnIndex * horizontalSpacing,
                    y: rowIndex * verticalSpacing
                },
                data: { label: course.name, lv: course },
            };
        });
    });
</script>

<div style:height="50rem">
  <SvelteFlow bind:nodes bind:edges {nodeTypes} fitView>
    <MiniMap />
    <Controls />
    <Background />
  </SvelteFlow>
</div>

<style>

</style>
