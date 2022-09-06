<template>
	<div class="center">
		<div class="wrapper" id="tasklist">  
			<h1 class="todolist_header">ToDo List</h1>
			<div v-if="!isEditing" class="todolist_subheader">
				<input class="todolist_placeholder" type="text" placeholder="Add TO DO" v-model="todo.itemName"  @keyup.enter="addTodo">
				<button class="todolist_btn add" value="add" @click="addTodo">ADD</button>
			</div>
			<div v-else class="todolist_subheader">
				<input class="todolist_placeholder" type="text" placeholder="Add TO DO" v-model="todo.itemName" >
				<button class="todolist_btn update" value="update" @click="updateTodo(index)">UPDATE</button>
			</div>
			<div class="todolist_descr">
				<div class="task">Task</div>
				<div class="action">Action</div>
			</div>
		<!-- <ul class="todolist_content">
			<li>
				<div class="todolist_content_text" v-for="todo in todos" :key="todo">{{todo}}</div>
			</li>
			<li>
				<button class="todolist_content_btn edit">EDIT</button>
			</li>
			<li>
				<button class="todolist_content_btn done">DONE</button>
			</li>
			<li>
				<button class="todolist_content_btn delete">DELETE</button>
			</li>
		</ul> -->
		<!-- <h1 v-for="todo in todos" :key="todo">{{todo}}</h1> -->
		<TodoContent 

		v-for="(todo, index) in todos"  
		:key="index" 
		:todo="todo"
		:index="index"
		@edit="editTodo"
		@delete="deleteTodo"
		/>
		<!-- 
		<TodoContent 
		v-for="(todo, index) in todos"  - обеъ
		:key="index" 
		:todo="todo"
		:index="index"
		@edit="editTodo"
		@delete="deleteTodo"
		/> 
		-->
		<!-- @done="done" -->
	

	</div>
	</div>
</template>

<style>

</style>

<script>
import { ref } from '@vue/reactivity'
import TodoContent from './TodoContent.vue'
export default {
  	name: 'To do list',
	components: {TodoContent},
	setup(){
		fetch('https://item-management.azurewebsites.net/api/items') // get запрос
		.then(response => response.json())
		.then(todo => todos.value = todo)
		 .then(json => console.log(json))
		// .then(json => { todos.value = json }) - выводит контент на экран
		const isEditing = ref(false)
		const selectedIndex = ref(null)
		const todo = ref({
			itemName: '',
			dateTimeUtc: '',
			isStrikeThrough: false
		})
		const todos = ref([])


		function deleteTodo(index, id) {
			todos.value.splice(index, 1)
			const deleteRequest = {
			method: 'DELETE',
			headers: {
			'Content-type': 'application/json; charset=UTF-8' 
			}}

			fetch("https://item-management.azurewebsites.net/api/items/" + id, deleteRequest) 

			.then(response => response.json())
			.then(data => console.log(data)) 

		}
		function editTodo(index, item) {
			todo.value = item
			selectedIndex.value = index
			isEditing.value = true

			// const putRequest = {
			// 	method: "PUT",
			// 	headers: { "Content-Type": "application/json" },
			// 	body: JSON.stringify(todo.value)
  			// }		
			// console.log(JSON.stringify(todo.value))
  			// fetch("https://item-management.azurewebsites.net/api/items/" + todos.value[index].id, putRequest)
			
			// console.log(index, item)

		}

		function addTodo() {
			if (todo.value.itemName.length === 0) {
				return
			}
			todos.value.push({itemName:todo.value.itemName, dateTimeUtc: (new Date()).toUTCString()})
			todo.value.dateTimeUtc = new Date().toISOString()
			const postRequest = {
				method: "POST",
				headers: { "Content-Type": "application/json" },
				body: JSON.stringify(todo.value)
  			}		
			console.log(JSON.stringify(todo.value))
  			fetch("https://item-management.azurewebsites.net/api/items", postRequest)
			.then(response => response.json(window.location.reload()))
			todo.value.itemName = ''
			
		}	

		function updateTodo() {
			todos.value.splice(selectedIndex.value, 1, {...todo.value})
			isEditing.value = false
			

			const putOptions = {
			method: "PUT",
			headers: { "Content-Type": "application/json" },
			body: JSON.stringify(todo.value)
			};
			fetch("https://item-management.azurewebsites.net/api/items/", putOptions)
			.then(response => response.json())
			console.log(JSON.stringify(todo.value))
			todo.value.itemName = ''

		}

		return {
			isEditing,
			selectedIndex,
			todo,
			todos,
			addTodo,
			updateTodo,
			editTodo,
			deleteTodo
		}
	},
	
}  
</script>
