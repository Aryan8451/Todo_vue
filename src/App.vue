<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([]);
const name = ref('');

const input_content = ref('');


const todos_asc = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt));
const todo_createdAt = ref('')
watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
});

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal));
}, {
  deep: true,
});

const addTodo = () => {
  if (input_content.value.trim() === '') {
    return;
  }

  const currentTime = new Date().getTime();
  todos.value.push({
    content: input_content.value,
   
    done: false,
    editable: false,
    createdAt: currentTime,
  });

  // Set todo_createdAt to the current time when a new todo is added
  todo_createdAt.value = new Date(currentTime).toLocaleString();

  input_content.value = '';

};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

const toggleDone = (todo) => {
  todo.done = !todo.done;
};

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2>CREATE A TODO</h2>
    </section>

    <section class="create-todo">
      

      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input 
          type="text" 
          name="content" 
          id="content" 
          placeholder="e.g. make a video"
          v-model="input_content" />
        
       

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <div v-for="todo in todos_asc" :key="todo.createdAt" :class="`todo-item ${todo.done && 'done'}`">
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
           
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
            <button class="done" @click="toggleDone(todo)">
              {{ todo.done ? 'Undone' : 'Done' }}
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
