<template>
	<div class="center">
			<div class="wrapper" id="tasklist">
		<h1 class="todolist_header">ToDo List</h1>
		<div v-if="!isEditing" class="todolist_subheader">
			<input class="todolist_placeholder" type="text" placeholder="Add TO DO" v-model="todo.itemName" >
			<button class="todolist_btn add" value="add" @click="addTodo">ADD</button>
		</div>
		<div v-else class="todolist_subheader">
			<input class="todolist_placeholder" type="text" placeholder="Add TO DO" v-model="todo.itemName" >
			<button class="todolist_btn update" value="update" @click="updateTodo">UPDATE</button>
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

		<div class="todolist_content" v-for="(todo, index) in todos" :key="todo">
			<p class="todolist_content_text" v-bind:class="{strikethrough:todo.status==='done'}">{{todo.itemName}}</p>
			<button class="todolist_content_btn edit" @click="editTodo(index, todo)" >EDIT</button>
			<button class="todolist_content_btn done" @click="done(index, todo)">DONE</button>
			<button class="todolist_content_btn delete" @click="deleteTodo(index)">DELETE</button>	
		</div>
	</div>
	</div>
</template>

<style>

</style>

<script>
export default {
  	name: 'To do list',
  	data() {
    	return {
			isEditing: false,
			selectedIndex: null,
			todo: {
				itemName: '',
				// status: 'open',
				dateTimeUtc: ''
			},
    		todos: []
    	}
  	},
	methods: {
		addTodo() {
			if (this.todo.itemName.length === 0) {
				return
			}
			this.todos.push({itemName:this.todo.itemName, dateTimeUtc: (new Date()).toUTCString()})
			this.todo.dateTimeUtc = new Date().toISOString()
			const requestOptions = {
				method: "POST",
				headers: { "Content-Type": "application/json" },
				body: JSON.stringify(this.todo)
  			};
			// console.log(JSON.stringify(this.todo))
  			fetch("https://item-management.azurewebsites.net/api/items", requestOptions)
			this.todo.itemName = ''
		},

		editTodo(index, todo) {
			this.todo = todo
			this.selectedIndex = index
			this.isEditing = true
		},

		done(index, todo) {
			todo.status='done'
			done = !done
		},

		updateTodo() {
			this.todos.splice(this.selectedIndex, 1, {...this.todo})
			this.isEditing = false
			this.todo.itemName = ''
			
		},

		deleteTodo(index) {
			this.todos.splice(index, 1)
		}
	}
}  
</script>
