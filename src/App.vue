<template>
  <div class="p-20 d-flex justify-content-center">
    <div class="card container">
      <div class="d-flex align-items-center mb-20">
        <h1 class="flex-fill">Todo List</h1>
        <select :value="todoStore.filter" @change="updateFilter($event)">
          <option value="all">Toutes</option>
          <option value="done">Finies</option>
          <option value="ondoing">En cours</option>
        </select>
      </div>
      <div class="d-flex align-items-center">
        <input v-model="input" type="text" class="flex-fill mr-20" />
        <button class="btn btn-primary mr-20" @click="addTodo">Ajouter</button>
      </div>
      <ul>
        <li
          v-for="todo in todoStore.filteredTodoList"
          class="card"
          :key="todo.content"
        >
          <div
            class="d-flex align-items-center"
            v-if="!todo.editMode"
            @click="updateTodo(todo._id!, { done: !todo.done })"
          >
            <input class="mr-20" :checked="todo.done" type="checkbox" />
            <span class="flex-fill">{{ todo.content }}</span>
            <button
              @click.stop="updateTodo(todo._id!, { editMode: true })"
              class="btn btn-primary mr-20"
            >
              Editer
            </button>
            <button @click.stop="deleteTodo(todo._id!)" class="btn btn-danger">
              Supprimer
            </button>
          </div>
          <div class="d-flex align-items-center" v-else>
            <TodoForm
              :content="todo.content"
              @cancel="updateTodo(todo._id!, { editMode: false })"
              @update="
                updateTodo(todo._id!, { editMode: false, content: $event })
              "
            />
          </div>
        </li>
      </ul>
      <!-- <button class="btn btn-danger" @click="deleteStore">dispose</button> -->
      <!-- <button class="btn btn-primary" @click="resetStore">reset</button> -->
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { useTodos } from './shared/stores/todoStore';
import { Todo } from './shared/interfaces/todo.interface';
import { Filter } from './shared/types';
import TodoForm from './components/TodoForm.vue';

const input = ref<string>('');

const todoStore = useTodos();
todoStore.fetchTodo();

function addTodo() {
  todoStore.addTodo(input.value);
  input.value = '';
}

function deleteTodo(todoId: string) {
  todoStore.deleteTodo(todoId);
}

function updateTodo(todoId: string, update: Partial<Todo>) {
  todoStore.updateTodo(todoId, update);
}

function updateFilter(event: Event) {
  const target = event.target as HTMLSelectElement;
  todoStore.updateFilter(target.value as Filter);
}

function deleteStore() {
  todoStore.$dispose();
}
function resetStore() {
  todoStore.$reset();
}
// todoStore.$subscribe((params) => {
//     console.log(params);
// })
// todoStore.$onAction((params) => {
//   console.log(params);
//   console.log('action is started');
//   after(() => {
//     console.log('action is done');
//   });
//   onError((error) => {
//     console.log('error');
//   });
// });
</script>

<style lang="scss">
@use './assets/scss/base.scss';

.container {
  width: 600px;
}

li {
  margin-bottom: 20px;
  cursor: pointer;
}
</style>
