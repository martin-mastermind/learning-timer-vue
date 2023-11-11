<template>
  <div>
    <input
      type="number"
      min="0"
      :max="maxValue"
      v-model="inputValue"
      :disabled="isDisabled"
    />
  </div>
</template>

<script setup lang="ts">
import { computed, ref, watch } from "vue";

const props = defineProps<{
  isHour?: boolean;
  isDisabled: boolean;
}>();
const emits = defineEmits<{
  (e: "changeValue", value: number): void;
}>();

const inputValue = ref("");

const maxValue = computed(() => (props.isHour ? 23 : 59));

watch(inputValue, (newValue) => {
  const parsedValue = +newValue;

  if (parsedValue < 0) {
    inputValue.value = "0";
  }

  if (parsedValue > maxValue.value) {
    inputValue.value = `${maxValue.value}`;
  }

  emits("changeValue", +inputValue.value);
});
</script>

<style scoped></style>
