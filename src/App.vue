<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">What's up, <input type="text" id="name" placeholder="Name here" v-model="name"></h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" name="content" id="content" placeholder="e.g. make a video" v-model="inputContent" />
        
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" id="category1" value="business" v-model="inputCategory" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input type="radio" name="category" id="category2" value="personal" v-model="inputCategory" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <div v-for="todo in sortedTodos" :key="todo.createdAt" :class="['todo-item', { 'done': todo.done }]">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="['bubble', todo.category]"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'

const name = ref('')
const inputContent = ref('')
const inputCategory = ref(null)
const todos = ref([])

const sortedTodos = ref([])

const addTodo = () => {
  if (!inputContent.value.trim() || !inputCategory.value) return
  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    editable: false,
    createdAt: Date.now()
  })
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true, immediate: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>
