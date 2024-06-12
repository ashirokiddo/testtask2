<template>
  <div class="movable-list">
    <div
        v-for="(item, listIndex) in modelValue"
        :key="listIndex"
        class="movable-list__wrap"
    >
      <div class="movable-list__item-wrap">
        <div
            v-for="(subItem) in item"
            :key="subItem.id"
            class="movable-list__item"
            :class="{'movable-list__item_selected': selectedItemsIds[listIndex].includes(subItem.id)}"
            @click="handleSelect(subItem, listIndex)"
        >
          {{subItem.name}}
        </div>
      </div>

      <div
          v-if="listIndex + 1 !== modelValue.length"
          class="movable-actions"
      >
        <app-button
            v-for="(item) in actions"
            :key="item.content"
            :disabled="isActionDisabled[listIndex][item.action]"
            view="primary"
            width="xs"
            @click="handleMove(item, listIndex)"
        >
          {{item.content}}
        </app-button>
      </div>
    </div>
  </div>
</template>

<script setup>
import AppButton from "@/components/atoms/app-button/AppButton.vue";
import {computed, ref} from "vue";

const props = defineProps({
  modelValue: {
    required: true,
    default: function () {
      return [];
    },
  },
});

const emit = defineEmits(["update:modelValue"]);

const selectedItemsIds = ref([]);
const virtualResult = ref([]);

const actions = [
  {action: "forwardAll", content: "=>>"},
  {action: "moveForwardSelected", content: "=>"},
  {action: "backAll", content: "<<="},
  {action: "backSelected", content: "<="},
];

const isActionDisabled = computed(() => {
  const result = [];

  for (let i = 0; i < props.modelValue.length; i++) {
    result.push({});
  }

  for (let i = 0; i < result.length; i++) {
    result[i].forwardAll = !props.modelValue[i].length;

    if (props.modelValue[i].length && result[i-1]) {
      result[i-1].backAll = false;
    } else {
      if (result[i-1]) {
        result[i-1].backAll = true;
      }
    }

    if (selectedItemsIds.value[i].length && result[i-1]) {
      result[i-1].backSelected = false;
    } else {
      if (result[i-1]) {
        result[i-1].backSelected = true;
      }
    }


    if (selectedItemsIds.value[i].length && result[i+1]) {
      result[i+1].moveForwardSelected = false;
    } else {
      result[i].moveForwardSelected = true;
    }
  }

  return result
});

function handleSelect(subItem, listIndex) {
  if (selectedItemsIds.value[listIndex].includes(subItem.id)) {
    const idx = selectedItemsIds.value[listIndex].findIndex((el) => {
      return el === subItem.id;
    });

    if (~idx) {
      selectedItemsIds.value[listIndex].splice(idx, 1);
    }
    return
  }

  selectedItemsIds.value[listIndex].push(subItem.id);
}

function moveForwardAll(actionItem, listIndex) {
  if (virtualResult.value[listIndex+1]) {
    virtualResult.value[listIndex+1].push(...virtualResult.value[listIndex]);
    virtualResult.value[listIndex].splice(0, virtualResult.value[listIndex].length);
    selectedItemsIds.value[listIndex] = [];
    emit("update:modelValue", virtualResult);
  }
}

function moveForwardSelected(actionItem, listIndex) {
  if (selectedItemsIds.value[listIndex].length && selectedItemsIds.value[listIndex+1]) {
    virtualResult.value[listIndex+1]
        .push(
            ...virtualResult.value[listIndex]
                .filter(el => {
                  return selectedItemsIds.value[listIndex].includes(el.id);
                })
        );

    selectedItemsIds.value[listIndex].forEach(elId => {

      const idxToRemove = virtualResult.value[listIndex].findIndex( subEl =>  elId === subEl.id);
      if (~idxToRemove) {
        virtualResult.value[listIndex].splice(idxToRemove, 1);
      }
    });

    selectedItemsIds.value[listIndex] = [];
    emit("update:modelValue", virtualResult);
  }

}

function backSelected(actionItem, listIndex) {
  virtualResult.value[listIndex]
      .push(
          ...virtualResult.value[listIndex+1]
              .filter(el => {
                return selectedItemsIds.value[listIndex+1].includes(el.id);
              })
      );

  selectedItemsIds.value[listIndex+1].forEach(elId => {
    const idxToRemove = virtualResult.value[listIndex+1].findIndex( subEl =>  elId === subEl.id);

    if (~idxToRemove) {
      virtualResult.value[listIndex+1].splice(idxToRemove, 1);
    }
  });

  selectedItemsIds.value[listIndex+1] = [];
  emit("update:modelValue", virtualResult);
}

function backAll(actionItem, listIndex) {
  virtualResult.value[listIndex].push(...virtualResult.value[listIndex+1]);
  virtualResult.value[listIndex+1].splice(0, virtualResult.value[listIndex].length);
  selectedItemsIds.value[listIndex] = [];
  emit("update:modelValue", virtualResult);
}

function handleMove(actionItem, listIndex) {
  switch (actionItem.action) {
    case "forwardAll": {
      moveForwardAll(actionItem, listIndex)
      break
    }

    case "moveForwardSelected": {
      moveForwardSelected(actionItem, listIndex);
      break
    }

    case "backSelected": {
      backSelected(actionItem, listIndex);
      break
    }

    case "backAll": {
      backAll(actionItem, listIndex);
      break
    }
  }
}

function init() {
  props.modelValue.forEach(() => {
    selectedItemsIds.value.push([]);
  });

  virtualResult.value = props.modelValue.map(el => el);
}


init();
</script>

<style scoped>
.movable-list {
  display: flex;
  .movable-list__wrap {
    display: flex;
    &:not(:last-child) {
      margin-right: 10px;
    }
  }

  .movable-list__item-wrap {
    border: 1px solid #000;
    border-radius: 6px;
    min-width: 200px;
    min-height: 200px;
  }

  .movable-list__item {
    width: 100%;
    text-align: center;
    padding: 4px 0px;
    cursor: pointer;
  }
  .movable-list__item:hover {
    background-color: #f3f5f7;
  }
  .movable-list__item.movable-list__item_selected {
    background-color: #b5b5b5;
  }
}
.movable-actions {
  display: flex;
  flex-direction: column;
  margin-left: 10px;

  .button:not(:last-child) {
    margin-bottom: 10px;
  }
}
</style>