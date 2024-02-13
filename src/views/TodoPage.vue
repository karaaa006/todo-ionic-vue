<template>
  <ion-page>
    <ion-content :fullscreen="true">
      <div class="container">
        <div class="logo-wrap">
          <ion-img src="/resources/todo.svg" alt="TODO Logo"></ion-img>
        </div>

        <ion-list class="todo-list">
          <ion-reorder-group
            :disabled="false"
            @ionItemReorder="handleReorder($event)"
          >
            <ToDoItem
              v-for="todo in todoList"
              :key="todo.id"
              :id="todo.id"
              :is-done="todo.isDone"
              :title="todo.title"
              @change="handleToggleTodo"
              @remove="handleRemoveTodo"
              @changeTitle="handleChangeTodoTitle"
            />
          </ion-reorder-group>
        </ion-list>

        <div class="input-wrap">
          <ion-input
            class="input-field"
            @keyup.enter="handleAddTodo(inputText)"
            aria-label="Enter your new ToDo..."
            placeholder="Enter your new ToDo..."
            v-model.trim="inputText"
          >
            <ion-button
              fill="clear"
              slot="end"
              aria-label="Add todo"
              @click="handleAddTodo(inputText)"
              color="dark"
              class="add-button"
            >
              <ion-icon
                slot="icon-only"
                :icon="addCircle"
                aria-hidden="true"
              ></ion-icon>
            </ion-button>
          </ion-input>
        </div>
      </div>
      <ion-toast
        :is-open="isToastOpen"
        message="Enter todo's title please!"
        :duration="3000"
        position="middle"
        @didDismiss="isToastOpen = false"
      ></ion-toast>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonPage,
  IonContent,
  IonInput,
  IonList,
  IonButton,
  IonIcon,
  IonImg,
  IonReorderGroup,
  IonToast,
} from "@ionic/vue";
import { addCircle } from "ionicons/icons";
import { onMounted, ref, watch } from "vue";
import ToDoItem from "@/components/ToDoItem.vue";
import { store } from "@/storage";
import { Todo } from "@/ts/interfaces";

const todoList = ref<Todo[]>([]);
const inputText = ref("");
const isToastOpen = ref(false);

onMounted(async () => {
  const data = await store.get("todos");

  todoList.value = JSON.parse(data);
});

const handleReorder = (event: CustomEvent) => {
  todoList.value = event.detail.complete(todoList.value);
};

const handleToggleTodo = async (id: Todo["id"]) => {
  const index = todoList.value.findIndex((todo) => todo.id === id);

  todoList.value[index].isDone = !todoList.value[index].isDone;
};

const handleChangeTodoTitle = async ({ id, title }: Omit<Todo, "isDone">) => {
  const index = todoList.value.findIndex((todo) => todo.id === id);

  todoList.value[index].title = title;
};

const handleAddTodo = async (title: string) => {
  if (!title) {
    isToastOpen.value = true;

    return;
  }

  todoList.value.push({ title: title, isDone: false, id: Date.now() });

  inputText.value = "";
};

const handleRemoveTodo = async (id: Todo["id"]) => {
  const index = todoList.value.findIndex((todo) => todo.id === id);

  todoList.value.splice(index, 1);
};

watch(
  todoList,
  async () => {
    await store.set("todos", JSON.stringify(todoList.value));
  },
  { deep: true }
);
</script>

<style scoped lang="scss">
.container {
  display: flex;
  flex-direction: column;

  padding: 63px 40px 30px;

  height: 100%;
}

.logo-wrap {
  width: 100%;
  max-width: 88px;

  margin: 0 auto 112px;
}

.todo-list {
  --ion-item-background: transparent;

  display: flex;
  flex-direction: column;
  gap: 10px;

  padding: 0 20px;
  margin: 0 -20px;

  padding-bottom: 100px;
}

.input-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  z-index: 2;

  width: 100%;

  padding: 10px 40px 30px;

  background: var(--ion-background-color);
}

.input-field {
  --padding-start: 15px !important;
  --highlight-color-focused: transparent;

  border-radius: 10px;

  font-size: 14px;

  background: var(--ion-color-light);
}

.add-button {
  opacity: 0.5;
}
</style>
