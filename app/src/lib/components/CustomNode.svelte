<script lang="ts">
  import { Handle, Position } from '@xyflow/svelte'
  import CourseCard from "$lib/components/CourseCard.svelte";

  export let data
</script>

<div class="course-node">
  <Handle type="source" position={Position.Left} />

  <CourseCard course={data.lv} />

  <Handle type="target" position={Position.Right} />
</div>

<style>
  .course-node {
      padding: 0.5rem;
      border: 0.1rem solid black;
      border-radius: 1rem;
      background: white;
      width: 15rem;
      text-align: center;
  }

  .course-node:hover {
      background: #eee;
  }
</style>