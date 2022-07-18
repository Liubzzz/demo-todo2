<script setup>
import TodoListItem from './TodoListItem.vue';
import { ref, reactive, toRefs, watch } from 'vue';
import axios from 'axios'
import { createDOMCompilerError } from '@vue/compiler-dom';

const response = await fetch('https://jsonplaceholder.typicode.com/todos');
let todos = await response.json();
todos = todos.slice(0, 10);
const reactiveTodos = ref(todos);

let company=ref()
let varnew=ref()

function created(){
    axios.get(`http://localhost:8081/company?name=${varnew.value}`)
  .then(response=> company.value=response.data.data.company)
  .catch(error => {
    this.errorMessage= error.message;
    console.error("there was an error", error);
  });
  console.log(company)
}

const counter = reactive({
    deleted: 0,
    updated: 0,
    added: 0
});
// const counter = ref(0);

function getUpdatedCounter() {
    return  counter.updated;
}

const newi= ref();
let isdisabled=ref(true);
watch(() => newi.value, (newValue, oldValue) => {
    if(newValue!= "")
    {isdisabled.value=false;}
    else
     {isdisabled.value=true;}


    console.log(`Counter updated ${oldValue} -> ${newValue}`);
});

function handleTodoItemDeleted(todoItemId) {
    reactiveTodos.value = reactiveTodos.value.filter(item => item.id !== todoItemId);
    counter.deleted++;
}

function handleTodoItemCompleted(todoItemId, completed) {
    reactiveTodos.value = reactiveTodos.value.map(todo => {
        if (todo.id === todoItemId) {
            return {
                ...todo,
                completed: completed
            }
        } else {
            return todo;
        }
    });
    counter.updated++;
}


function handleTodoItemAdded() {
    const additem={
        id: 1,
        title: newi.value,
        completed: false,
        userId: 1
    }
    reactiveTodos.value.push(additem);
    counter.added++;
}


const checkbox = ref(true);

watch(checkbox, newValue => {
    console.log(`checkbox: ${newValue}`);
});
</script>

<template>
    <span>Two-way data binding (v-model):</span>
    <input type="checkbox" v-model="checkbox"  />
    <div>
        <span><input  v-model ="newi" type="text" /></span>
    <div><input v-model="varnew" type="text"></div>
         <button  @click= "handleTodoItemAdded"  :disabled="isdisabled" class="button-33" role="button">Add</button>
         <button  @click= "created()"  class="button-33" role="button">Create</button>
         <p> {{company.name}}</p>

    
    
    
    </div>
   

    <p>Updated: {{ counter.updated }}</p>
    <p>Deleted: {{ counter.deleted }}</p>
    <p>Added: {{ counter.added }}</p>
    <div class="todo-list">
        <TodoListItem
            v-for="todo of reactiveTodos"
            :key="todo.id"
            :id="todo.id"
            :title="todo.title"
            :completed="todo.completed"
            @todo-item-deleted="handleTodoItemDeleted(todo.id)"
            @todo-item-completed="completed => handleTodoItemCompleted(todo.id, completed)"
        />
    </div>
</template>

<style scoped>
.todo-list {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
}






/* CSS button*/
.button-33 {
  background-color: #c2fbd7;
  border-radius: 100px;
  box-shadow: rgba(44, 187, 99, .2) 0 -25px 18px -14px inset,rgba(44, 187, 99, .15) 0 1px 2px,rgba(44, 187, 99, .15) 0 2px 4px,rgba(44, 187, 99, .15) 0 4px 8px,rgba(44, 187, 99, .15) 0 8px 16px,rgba(44, 187, 99, .15) 0 16px 32px;
  color: green;
  cursor: pointer;
  display: inline-block;
  font-family: CerebriSans-Regular,-apple-system,system-ui,Roboto,sans-serif;
  padding: 7px 20px;
  text-align: center;
  text-decoration: none;
  transition: all 250ms;
  border: 0;
  font-size: 16px;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
}

.button-33:hover {
  box-shadow: rgba(44,187,99,.35) 0 -25px 18px -14px inset,rgba(44,187,99,.25) 0 1px 2px,rgba(44,187,99,.25) 0 2px 4px,rgba(44,187,99,.25) 0 4px 8px,rgba(44,187,99,.25) 0 8px 16px,rgba(44,187,99,.25) 0 16px 32px;
  transform: scale(1.05) rotate(-1deg);
}





/*checkbox */











</style>


