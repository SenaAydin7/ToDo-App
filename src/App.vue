<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))


watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				Hi, <input type="text" id="name" placeholder="Name here" v-model="name">
			</h2>
			<h3>CREATE A TODO</h3>
		</section>

		<section class="create-todo">

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's on your ToDo list?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="Type a ToDo"
					v-model="input_content" />
				
				<h4>Pick a category</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="school"
							v-model="input_category" />
						<span class="bubble school"></span>
						<div>School</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category3" 
							value="home"
							v-model="input_category" />
						<span class="bubble home"></span>
						<div>Home</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category4" 
							value="health"
							v-model="input_category" />
						<span class="bubble health"></span>
						<div>Health</div>
					</label>

				</div>

				<input type="submit" value="Add ToDo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'personal' 
								? 'personal' 
								: 
							todo.category == 'school' 
								? 'school' 
								:  
							todo.category == 'home' 
								? 'home' 
								: 'health'
							
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

