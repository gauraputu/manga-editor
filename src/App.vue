<script setup lang="ts">
import { ref, reactive } from "vue";
import { invoke } from "@tauri-apps/api/core";
import { open } from '@tauri-apps/plugin-dialog';
import { readDir } from '@tauri-apps/plugin-fs';
import Table from './component/Table.vue';

const greetMsg = ref("");
const name = ref("");
const files = reactive([])

async function greet() {
  // Learn more about Tauri commands at https://tauri.app/develop/calling-rust/
  greetMsg.value = await invoke("greet", { name: name.value });
}

const handleChooseFolderButton = async (): Promise<string | string[]> => {
  const directoryPath = await open({
    multiple: false,
    directory: true,
  });

  const filesInDirectory = await getFilesInDirectory(directoryPath)
  files.push(...filesInDirectory.filter(file => !files.includes(file)));
  console.log(filesInDirectory)
  console.log(files)
}

async function getFilesInDirectory(path) {
  try {
    const filesInPath = await readDir(path, { recursive: false });
    for (let obj of filesInPath) {
      obj['path'] = path
    }
    return filesInPath.filter(obj => obj.isFile)
  } catch (error) {
    console.error('Error reading directory:', error);
    return [];
  }
}
</script>

<template>
  <main class="container">
    <button id="choose-folder-button" @click="handleChooseFolderButton"
      class="btn btn-md btn-primary">ChooseFolder</button>
    <Table v-model="files" />
  </main>
</template>
