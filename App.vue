<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([ref])
const name = ref('')

const input_content = ref('')
const input_priority = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_priority.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    priority: input_priority.value,
    done: false,
    createdAt: new Date().getTime()
  })
  
  input_content.value = ''
  input_priority.value = null
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true})

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>

<main class="app">
  
  <section class="task">

    <form @submit.prevent="addTodo">
      <div class="heading">
        <h1><span>TODO APP</span></h1> 
        <h1>ADD YOUR <em>TASK</em></h1> 
      </div>
      <input type="text" 
      placeholder="e.g. make appointment"
      v-model="input_content" />

      <h4>CHOOSE PRIORITY</h4>

      <div class="options">

        <label>
          <input
          type="radio" name="priority" value="High" id="priority1"
          v-model="input_priority" />
          <span class="bubble High"></span>
          <div>High Priority</div>
        </label>

        <label>
          <input
          type="radio" name="priority" value="Low" id="priority2" 
          v-model="input_priority" />
          <span class="bubble Low"></span>
          <div>Low Priority</div>
        </label>

      </div>

      <input type="submit" 
        value="Add todo" />
    </form>

    </section>
 
    <section class="todo-list">
			<h3>TASKS</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.priority == 'High' 
								? 'High' : 'Low'
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
		</section>

	</main>
</template>


