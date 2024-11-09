<script setup lang="ts">
import { readDir } from '@tauri-apps/plugin-fs';
import { ref } from 'vue'

const props = defineProps({
  modelValue: Array
});

</script>
<template>
    <div class="overflow-x-auto">
        <table class="table">
            <!-- head -->
            <thead>
                <tr>
                    <th>No</th>
                    <th>Name</th>
                    <th>Location</th>
                </tr>
            </thead>
            <tbody>
                <!-- row 1 -->
                <tr class="hover" v-for="(file, index) in modelValue" :key="index" @click="$emit('select-file', file)">
                    <th>{{ index +1 }}</th>
                    <td>{{ file.name }}</td>
                    <td>{{ file.path }}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>