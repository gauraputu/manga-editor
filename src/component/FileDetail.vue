<script setup>
import { computed, toRef, ref } from 'vue';
const props = defineProps({
    modelValue: {
        type: Object,
        default: () => ({}),
    }
});

const name = computed({
  get() {
    return props.modelValue?.name || ''; // Fallback to empty string if null/undefined
  },
  set(value) {
    if (props.modelValue) {
      props.modelValue.name = value;
    }
  },
});
</script>

<template>
    <div class="flex flex-column">
        <div>
            <label class="form-control w-full max-w-xs">
                <div class="label">
                    <span class="label-text">File Name</span>
                </div>
                <input type="text" placeholder="Type here" class="input input-bordered w-full max-w-xs" v-model="name"/>
            </label>
            <label class="form-control w-full max-w-xs">
                <div class="label">
                    <span class="label-text">Series Name</span>
                </div>
                <input type="text" placeholder="Type here" class="input input-bordered w-full max-w-xs" value="" />
            </label>
        </div>
        <div class="artboard phone-1">image will be here</div>
    </div>
</template>