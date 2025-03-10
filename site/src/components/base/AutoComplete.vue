<template>
  <OnClickOutside
    relative
    @click="isFocus = true"
    @trigger="
      () => {
        isFocus = false;
        editTimes = 0;
        searchTerm = lastSearchTerm;
      }
    "
  >
    <div
      class="flex hstack h-9 space-x-2 px-2 py-1 rounded border"
      :class="[
        isFocus && 'border-black dark:border-white',
        !isFocus && 'border-c'
      ]"
    >
      <input
        v-model="searchTerm"
        class="w-full rounded outline-none bg-transparent capitalize"
        @input="editTimes++"
      />
      <div text-gray-500>
        <div v-show="isFocus">
          <span class="iconify text-lg" data-icon="ic:sharp-arrow-drop-up" />
        </div>
        <div v-show="!isFocus">
          <span class="iconify text-lg" data-icon="ic:sharp-arrow-drop-down" />
        </div>
      </div>
    </div>
    <Dropdown
      v-show="isFocus && filteredItems.length > 0"
      class="absolute z-20 w-full border border-c rounded"
      :items="filteredItems"
      @select="handleSelect"
    />
  </OnClickOutside>
</template>

<script lang="ts" setup>
import { OnClickOutside } from "@vueuse/components";
import type { DropdownItem } from "./types";

const props = defineProps<{
  items: Array<DropdownItem>;
  default?: string;
}>();

const emit = defineEmits(["select"]);

const isFocus = ref(false);
const editTimes = ref(0);

const searchTerm = ref<string>(props.default || props.items[0].text);
const lastSearchTerm = ref(searchTerm.value);

const handleSelect = (text: string) => {
  searchTerm.value = text;
  lastSearchTerm.value = text;
  emit("select", text);
};

const filteredItems = computed(() =>
  editTimes.value > 0
    ? props.items.filter((item) => item.text.includes(searchTerm.value))
    : props.items
);
</script>
