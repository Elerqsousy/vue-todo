<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')
const todoInput = ref('')
const category = ref(null)

const todosAsc = computed(() => (
  [...todos.value]
    .sort((a, b) => a.createdAt - b.createdAt)
    .sort((a, b) => a.completed - b.completed)
))

const addTodo = () => {
  if (todoInput.value.trim() !== '' && category.value !== null) {
    todos.value.push({
      content: todoInput.value,
      createdAt: new Date().getTime(),
      category: category.value,
      completed: false
    })
  }
  todoInput.value = ''
  category.value = null
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
  localStorage.setItem('todosList', JSON.stringify(newVal))
}, { deep: true })

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todosList')) || []
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          name="todo-content"
          id="content"
          placeholder="e.g. make a video"
          v-model="todoInput"
        />
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="pesonal"
              v-model="category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div v-if="todosAsc.length" class="list" id="todo-list">
        <div v-for="todo in todosAsc" :class="`todo-item ${todo.completed && 'done'}`" :key="todo.createdAt">
          <label>
            <input type="checkbox" v-model="todo.completed" />
            <span :class="`bubble ${
              todo.category === 'business'
              ? 'business'
              : 'personal'
              }`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
      <span v-else>No todos available to display...</span>
    </section>
  </main>
</template>
