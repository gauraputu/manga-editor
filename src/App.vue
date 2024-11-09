<script setup lang="ts">
import { ref, reactive } from "vue";
import { invoke } from "@tauri-apps/api/core";
import { open } from '@tauri-apps/plugin-dialog';
import { readDir, readFile, BaseDirectory } from '@tauri-apps/plugin-fs';
import Table from './component/Table.vue';
import FileDetail from './component/FileDetail.vue';
import JSZip from "jszip";

const greetMsg = ref("");
const name = ref("");
const files = reactive([])
const selectedFile = ref(null);

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

async function readZipFromPath(filePath) {
  const zip = new JSZip();
  const fileData = await readFile(filePath, {
    baseDir: BaseDirectory.Home,
  }); // Read file from path in binary
  const zipContent = await zip.loadAsync(fileData);

  zipContent.forEach(async (relativePath, zipEntry) => {
    console.log("File:", relativePath);
    if (!zipEntry.dir) {
      const fileContent = await zipEntry.async("text");
      console.log("Content:", fileContent);
    }
  });
}

function handleTableSelectFile(file) {
  selectedFile.value = file;
  const zipFile = readZipFromPath(file.path+'/'+file.name)
}
</script>

<template>
  <main class="container">
    <button id="choose-folder-button" @click="handleChooseFolderButton"
      class="btn btn-md btn-primary">ChooseFolder</button>
    <div class="grid grid-cols-2 gap-4">
      <Table v-model="files" @select-file="handleTableSelectFile" />
      <FileDetail v-model="selectedFile" />
    </div>
  </main>
</template>
