<template>

  <main class="app">
    <section class="greeting">
      <h2 class="title">
        😊What's up, <input type="text" placeholder="Input task" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE TASKS</h3>

      <form @submit.prevent="addTask">
        <h3>What's on your Task List</h3>
        <input type="text"
               placeholder="e.g. email your boss"
               v-model="input_text"/>
        <h4>Choose a Category</h4>

        <div class="options">

          <label>
            <input type="radio"
                   name="category"
                   value="business"
                   v-model="input_category"/>
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input type="radio"
                   name="category"
                   value="personal"
                   v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add Task"/>
      </form>

    </section>

    <section class="todo-list">
      <h3>TASK LIST</h3>
      <div class="list">

        <div v-for="task in tasks_asc" :class="`todo-item ${task.completed && 'done'}`">
          <label >
            <input type="checkbox" v-model="task.completed"/>
            <span :class="`bubble ${task.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="task.task">
          </div>

          <div class="actions">
            <button class="delete" @click="removeTask(task)">Delete</button>
          </div>

        </div>

      </div>
    </section>
    <div class="actions">
      <button class="delete" @click="resetTracker">Reset Tracker</button>
    </div>
  </main>

</template>


<script setup>
import {ref, onMounted, computed, watch} from "vue";
// ref --> for state management
// onMounted --> to execute a command once the page starts
// computed --> for mathematical computing
// watch --> an observable which watches for page changes

const tasks = ref([]);
const name = ref('');
const input_text = ref('');
const input_category = ref(null);

const tasks_asc = computed(() => tasks.value.sort((a, b) => {
  return b.createdAt - a.createdAt;
}))

const addTask = () => {
  if (input_text.value.trim() === '' || input_category === null) {
    return
  }

  tasks.value.push({
    task: input_text.value,
    category: input_category.value,
    completed: false,
    createdAt: new Date().getTime(),
  })

  input_text.value = '';
  input_category.value = null;
}

const resetTracker =() => {
  return localStorage.clear();
}

watch(tasks, (newValue) => {
  localStorage.setItem("tasks", JSON.stringify(newValue));
}, {deep: true});

const removeTask =(task) =>{
  tasks.value = tasks.value.filter((t)=> t !== task)
}

watch(name, (newValue) => {
  localStorage.setItem("name", newValue);
});


onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  tasks.value = JSON.parse(localStorage.getItem('tasks')) || [];
});

</script>