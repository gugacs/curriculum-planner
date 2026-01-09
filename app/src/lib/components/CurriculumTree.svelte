<script lang="ts">
    import { SvelteFlow, MiniMap, Controls, Background } from '@xyflow/svelte';
    import '@xyflow/svelte/dist/style.css';
    import { curriculumStore } from "$lib/states/curriculum.svelte";
    import CustomNode from "$lib/components/CustomNode.svelte";

    const nodeTypes = { custom: CustomNode };

    let nodes = $state.raw([]);
    let edges = $state.raw([]);

    $effect(() => {
        const verticalSpacing = 200;
        const horizontalSpacing = 350;

        // group courses by semester
        const coursesBySemester = $curriculumStore.courses.reduce((acc, course) => {
            let semester = parseInt(course.recommended_semester); // parseInt to handle strings like "2;2" and convert to a number

            if (isNaN(semester)) {
                semester = 0; // default to semester 0 if parsing fails
            }
            if (!acc[semester]) {
                acc[semester] = [];
            }
            acc[semester].push(course);
            return acc;
        }, {});

        console.log(coursesBySemester);

        // create nodes from the grouped structure
        nodes = Object.keys(coursesBySemester)
            .sort((a, b) => Number(a) - Number(b)) // Ensure semesters are in numerical order
            .flatMap((semester, columnIndex) => {
                const coursesInSemester = coursesBySemester[semester];

                return coursesInSemester.map((course, rowIndex) => ({
                    id: `${course.id}-${semester}-${rowIndex}`, // Unique ID
                    type: 'custom',
                    position: {
                        x: columnIndex * horizontalSpacing,
                        y: rowIndex * verticalSpacing
                    },
                    data: { label: course.name, lv: course },
                }));
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
