<template>
    <div class="todolist_content" >
		<p class="todolist_content_text" v-bind:class="{strikethrough:todo.isStrikeThrough}">{{todo.itemName}}</p>
		<button class="todolist_content_btn edit" @click="editTodo(index, todo)">EDIT</button>
		<button class="todolist_content_btn done" @click="done(index, todo, todo.id)">DONE</button>
		<button class="todolist_content_btn delete" @click="deleteTodo(index, todo.id)">DELETE</button>	
	</div>
</template>

<script>
export default {
    name: 'TodoContent',
    props: {                         
        todo: {
            type: Object,
            required: true,
            default: () => ({})            
        },
        index: {
            type: Number,    // type number - потому что число
            required: true, 
            default: 0   
        }

    },
    emits:[
        'edit',
        'done',
        'delete'
    ],
    setup(props, {emit}) {
        function editTodo(index, item) {
            emit('edit', index, item)    // 'имя', передаю index и item
            }
        function done(index, todo, id) {          
			todo.isStrikeThrough = !todo.isStrikeThrough
            console.log(todo.isStrikeThrough)         
            if (todo.isStrikeThrough === false) {
                deleteTodo(index, id)   
            } 
            emit('done', todo)
		}   
        function deleteTodo(index, id) {
            emit('delete', index, id) // 'имя', передаю index
		}
        return {
            editTodo,
            done,
            deleteTodo
        }
    }
}
</script>


