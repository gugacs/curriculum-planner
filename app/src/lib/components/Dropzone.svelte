<!--
  Code based on
  - Name: Svelte Tauri FileDrop
  - By: Kasper Henningsen
  - Licensed under: MIT License
  - Source: https://github.com/probablykasper/svelte-tauri-filedrop#usage
-->

<script lang="ts">
  import { readTextFile, exists } from "@tauri-apps/plugin-fs";
  import FileDrop from "svelte-tauri-filedrop"
  import Papa from "papaparse";
  import type { Course, Curriculum, Module } from "$lib/types/data";

  let fileContent: string = "None"
  const readFile = async (path: string) => {
    try {
      // NOTE: https://v2.tauri.app/plugin/file-system
      const fileExists = await exists(path);

      if(!fileExists) {
        console.error(`File ${path} does not exist`)
        return null;
      }

      const content = await readTextFile(path);
      return content
    } catch(error) {
      console.error(error)
      return null;
    }

    return null;
  }

  const parseFile = async (content: string) => {
    // NOTE: https://www.papaparse.com/docs
    const result = Papa.parse(content, {
      delimiter: "",	// auto-detect
      newline: "",	// auto-detect
      quoteChar: '"',
      escapeChar: '"',
      header: true,
      transformHeader: undefined,
      dynamicTyping: false,
      preview: 0,
      encoding: "",
      worker: false,
      comments: false,
      step: undefined,
      complete: undefined,
      error: undefined,
      download: false,
      downloadRequestHeaders: undefined,
      downloadRequestBody: undefined,
      skipEmptyLines: false,
      chunk: undefined,
      chunkSize: undefined,
      fastMode: undefined,
      beforeFirstChunk: undefined,
      withCredentials: undefined,
      transform: undefined,
      delimitersToGuess: [',', '\t', '|', ';', Papa.RECORD_SEP, Papa.UNIT_SEP],
      skipFirstNLines: 0
    });

    return result;
  }

  const parseToDataStructure = (data) => {
    // Storing unique modules by first iterating once over the list of courses
    let modules: Module[] = [];

    for (let i = 0; i < data.length; i++) {
      const module: Module = {
        code: data[i]["module_code"],
        name: data[i]["module_name"],
        credits: -1
      }

      if (module.name.includes(",")) continue;
      if (modules.find(m => m.code === module.code)) continue;

      modules.push(module);
    }
    console.log(modules)

    // Filling in all the possible data for courses
    let courses: Course[] = [];
    for (let i = 0; i < data.length; i++) {
      const rawCourse = data[i];
      const course: Course = {
        id: rawCourse["course_id"],
        name: rawCourse["course_name"],
        module: [],
        subcategory: rawCourse["subcategory"],
        type: rawCourse["type"],
        credits: parseFloat(rawCourse["credits"]),
        required: parseInt(rawCourse["required"]),
        availability: rawCourse["availability"],
        recommended_semester: rawCourse["recommended_semester"],
        prerequisites: [],
        frequency: rawCourse["frequency"],
        language: rawCourse["language"],
        description: rawCourse["description"],
        url: rawCourse["url"]
      };

      if (rawCourse["module_code"].includes(",")) {
        const moduleCodes = rawCourse["module_code"].split(",");
        const moduleNames = rawCourse["module_name"].split(",");

        if (moduleCodes.length != moduleNames.length) {
          console.error(`Mismatched length module codes:names of ${course.id}`);
          continue;
        }

        for (let j = 0; j < moduleCodes.length; j++) {
          course.module.push(modules.find(m => m.code === moduleCodes[j])!);
        }
      } else {
        course.module = [modules.find(m => m.code === rawCourse["module_code"])!];
      }
      courses.push(course);
    }

    console.log(courses)

    for (let i = 0; i < data.length; i++) {
      const rawCourse = data[i];
      const prerequsites = rawCourse["prerequisites"];
      if (!prerequsites) continue;
      if (prerequsites.includes(",")) {
        const prerequisiteIDs = prerequsites.split(",");
        for (let j = 0; j < prerequisiteIDs.length; j++) {
          const prereqCourse = courses.find(c => c.id === prerequisiteIDs[j]);
          if (!prereqCourse) continue;

          courses[i].prerequisites.push(prereqCourse);
        }
      } else {
        const prereqCourse = courses.find(c => c.id === prerequsites);
        if (!prereqCourse) continue;

        courses[i].prerequisites.push(prereqCourse);
      }
    }

    console.log(courses)
    let curriculum: Curriculum = {
      credits: -1,
      modules: modules,
      courses: courses
    }

    console.log(curriculum);

    return curriculum;
  }

  const handleDrop = async (paths: string[]) => {
    if(paths.length != 1) return;
    const path = paths[0];

    // TODO: Open and read file content and return here again
    const content = await readFile(path);
    if (!content) return;
    // TODO: Parse file content to custom data structure
    const data = await parseFile(content);
    const courses = data.data;
    const curriculum = parseToDataStructure(courses)
    // TODO: Store data structure in a global svelte store
  }
</script>

<FileDrop extensions={["csv"]} handleFiles={handleDrop} let:files>
  <div class="dropzone" class:droppable={files.length == 1}>
    <h2>Drop CSV files</h2>
  </div>
</FileDrop>

<p>{fileContent}</p>

<style>
.dropzone {
  margin: 20px;
  padding: 20px;
  background: #eee;
}
.droppable {
  background: #d6dff0;
}
</style>
