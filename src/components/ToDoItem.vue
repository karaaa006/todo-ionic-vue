<template>
  <ion-item-sliding
    ref="sliding"
    class="todo"
    :class="{ 'is-checked': $props.isDone }"
  >
    <ion-item-options class="options" side="start">
      <ion-item-option @click="handleEdit" color="dark"> Edit </ion-item-option>
    </ion-item-options>
    <ion-item class="todo-item" collapse="collapse" lines="none">
      <ion-input
        v-if="isEditMode"
        @keyup.enter="handleSetTitle"
        v-model="newTitle"
        ref="newTitleInput"
      >
        <ion-button
          :disabled="!newTitle"
          fill="clear"
          slot="start"
          aria-label="Add todo"
          @click="handleSetTitle"
          color="dark"
          class="add-button"
        >
          <ion-icon
            slot="icon-only"
            :icon="checkmarkCircle"
            aria-hidden="true"
          ></ion-icon> </ion-button
      ></ion-input>

      <ion-checkbox
        v-else
        class="checkbox"
        @ion-change="() => $emit('change', id)"
        :checked="$props.isDone"
        label-placement="end"
        justify="start"
        color="dark"
      >
        {{ $props.title }}
      </ion-checkbox>
      <div class="buttons">
        <ion-button
          fill="clear"
          color="dark"
          @click="() => $emit('remove', id)"
        >
          <ion-icon :icon="trash" class="remove-icon"></ion-icon>
        </ion-button>
        <ion-reorder></ion-reorder>
      </div>
    </ion-item>
  </ion-item-sliding>
</template>

<script setup lang="ts">
import { ref } from "vue";
import {
  IonCheckbox,
  IonIcon,
  IonItem,
  IonButton,
  IonReorder,
  IonItemSliding,
  IonItemOption,
  IonItemOptions,
  IonInput,
} from "@ionic/vue";
import { trash, checkmarkCircle } from "ionicons/icons";
import { Todo } from "@/ts/interfaces";

interface Props extends Todo {}

interface Emits {
  (e: "change", id: Todo["id"]): void;
  (e: "changeTitle", arg: Omit<Todo, "isDone">): void;
  (e: "remove", id: Todo["id"]): void;
}

const props = defineProps<Props>();
const emit = defineEmits<Emits>();

const isEditMode = ref(false);
const newTitle = ref("");
const newTitleInput = ref<typeof IonInput | null>(null);
const sliding = ref<typeof IonItemSliding | null>(null);

const handleEdit = async () => {
  isEditMode.value = true;
  newTitle.value = props.title;

  sliding.value?.$el.closeOpened();
};

const handleSetTitle = async () => {
  if (!newTitle.value) {
    return;
  }

  isEditMode.value = false;

  emit("changeTitle", { id: props.id, title: newTitle.value });
};
</script>

<style lang="scss" scoped>
.options {
  overflow: hidden;
  border-radius: 10px 0 0 10px;
}

.todo {
  border-radius: 10px;

  background: var(--ion-background-color);

  box-shadow: 0px 4px 16px 0px #0000001a;

  &:not(:last-child) {
    margin-bottom: 10px;
  }
}

.todo-item {
  border-radius: 10px;
  background: var(--ion-background-color);
}

.todo.is-checked {
  opacity: 0.2;
}

.checkbox {
  --size: 25px;

  width: 100%;

  font-size: 14px;
  font-weight: 700;

  &::part(label) {
    white-space: unset;
  }
}

.buttons {
  display: flex;
  align-items: center;
}

.remove-icon {
  opacity: 0.3;
}
</style>
