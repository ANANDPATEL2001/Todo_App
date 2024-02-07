<template>
    <div>
        <!-- 'v-model' is used for Dynamic Data Binding
        @click is shorthand form of v-on:click
        :class is shorthand form of v-bind:class (used for Dynamic Binding of properties) -->
        <center><input type="text" placeholder="Type Here" class="todo-input" v-model="newTodo" @keyup.enter="addTodo">
        </center>

        <!-- <TransitionGroup name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown"> -->

        <!-- <div class="card" style="width: 18rem;">
                <img src="#" class="card-img-top" alt="...">
                <div class="card-body">
                    <h5 class="card-title">Card title</h5>
                    <p class="card-text">Some quick example text to build on the card title and make up the bulk of the
                        card's content.</p>
                    <a href="#" class="btn btn-primary">Go somewhere</a>
                </div>
            </div> -->

        <div class="todo-item">
            <div v-for="(todo, index) in todosFiltered" :key="todo.id">

                <div class="card main-card">
                    <div class="card-header">
                        <span class="delete-icon">
                            <!-- <input type="checkbox" v-model="todo.completed"> -->
                            <svg xmlns="http://www.w3.org/2000/svg" width="25" height="25" fill="currentColor"
                                class="bi bi-check2-circle" viewBox="0 0 16 16"
                                :class="{ 'blueColor': todo.completed, 'redColor': !todo.completed }"
                                @click="todo.completed = !todo.completed">
                                <path
                                    d="M2.5 8a5.5 5.5 0 0 1 8.25-4.764.5.5 0 0 0 .5-.866A6.5 6.5 0 1 0 14.5 8a.5.5 0 0 0-1 0 5.5 5.5 0 1 1-11 0" />
                                <path
                                    d="M15.354 3.354a.5.5 0 0 0-.708-.708L8 9.293 5.354 6.646a.5.5 0 1 0-.708.708l3 3a.5.5 0 0 0 .708 0z" />
                            </svg>
                        </span>
                        <span class="remove-item" @click="removeTodo(index)">
                            &times;
                        </span>
                    </div>
                    <img src="https://source.unsplash.com/random/" class="card-img-top card-img" alt="Loading the Image">
                    <div class="card-body">
                        <!-- <h4 class="card-title">Card title</h4> -->
                        <p class="card-text">

                        <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label"
                            :class="{ completed: todo.completed }">
                            {{ todo.title }}
                        </div>
                        <input v-else class="todo-item-edit" type="text" v-model="todo.title" placeholder="Hello"
                            @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus>

                        </p>
                        <!-- <a href="#" class="btn btn-primary">Go somewhere</a> -->
                    </div>
                </div>
            </div>





            <!-- <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label"
                        :class="{ completed: todo.completed }">
                        {{ todo.title }}
                    </div>
                    <input v-else class="todo-item-edit" type="text" v-model="todo.title" placeholder="Hello"
                        @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus> -->
        </div>
        <!-- </TransitionGroup> -->


        <div class="extra-container">
            <div><label>
                    <input class="mx-2" :checked="!anyRemaining" @change="checkAllTodos" type="checkbox">Check All
                </label></div>
            <div>{{ remaining }} Item Left</div>
        </div>


        <div class="extra-container">
            <div>
                <button type="button" class="btn btn-outline-primary" :class="{ active: filter == 'all' }"
                    @click="filter = 'all'">All</button>
                <button type="button" class="btn btn-outline-primary" :class="{ active: filter == 'active' }"
                    @click="filter = 'active'">Active</button>
                <button type="button" class="btn btn-outline-primary" :class="{ active: filter == 'completed' }"
                    @click="filter = 'completed'">Completed</button>
            </div>
            <div>
                <transition name="fade">
                    <button type="button" class="btn btn-outline-secondary" v-if="showClearCompletedButton"
                        @click="clearCompleted">Clear Completed</button>
                </transition>
            </div>
        </div>
    </div>
</template>
  
<script>
import { computed } from 'vue'

export default {
    name: 'todo-list',
    data() {
        return {
            newTodo: '',
            beforeEditCache: '',
            idForTodo: 4,
            filter: 'all',
            // taskDoneColor: 'blueColor',
            todos: [
                {
                    id: 1,
                    title: "Take over world",
                    completed: false,
                    editing: false,
                },
                {
                    id: 2,
                    title: "Have a nice launch",
                    completed: false,
                    editing: false,
                },
                {
                    id: 3,
                    title: "Take a deep sleep",
                    completed: false,
                    editing: false,
                },
            ]
        }
    },

    // The difference bw the 'methods' and 'computed' property is that 
    // 'computed' property is used to derive the data from another data but
    // it should not mutate any other data 
    // it should not accept ant parameters 
    // it should alwayse return something
    computed: {
        remaining() {
            return this.todos.filter(todo => !todo.completed).length
        },

        anyRemaining() {
            return this.remaining != 0
        },

        todosFiltered() {
            if (this.filter == 'all')
                return this.todos
            else if (this.filter == 'active')
                return this.todos.filter(todo => !todo.completed)
            else if (this.filter == 'completed')
                return this.todos.filter(todo => todo.completed)

            return this.todos
        },

        showClearCompletedButton() {
            return this.todos.filter(todo => todo.completed).length > 0
        }
    },

    directives: {
        focus: {
            inserted: function (el) {
                el.focus()
            }
        }
    },

    methods: {
        addTodo() {
            if (this.newTodo.trim().length == 0)
                return

            this.todos.push({
                id: this.idForTodo,
                title: this.newTodo,
                completed: false,
                editing: false,
            })

            this.newTodo = ''
            this.idForTodo++
        },

        removeTodo(index) {
            this.todos.splice(index, 1)
        },

        editTodo(todo) {
            this.beforeEditCache = todo.title
            todo.editing = true
        },

        doneEdit(todo) {
            if (todo.title.trim() == '')
                todo.title = this.beforeEditCache
            todo.editing = false
        },

        cancelEdit(todo) {
            todo.title = this.beforeEditCache
            todo.editing = false
        },

        checkAllTodos() {
            this.todos.forEach((todo) => todo.completed = event.target.checked)
        },

        clearCompleted() {
            this.todos = this.todos.filter(todo => !todo.completed)
        }
    }
}
</script>
  

<style>
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");

.todo-input {
    width: 70%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;

    /* &:focus {
        outline: 0;
    } */
}

.todo-input:focus {
    outline: 0;
}

.todo-item {
    margin-bottom: 13px;
    /* display: flex;
    align-items: center;
    justify-content: space-between; */
    animation-duration: 0.3s;

    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 1rem;
}

.remove-item {
    cursor: pointer;
    margin-left: 14px;
    float: right;
    font-size: 30px;
    padding: 0 5px;


    /* &:hover {
        color: black;
    } */
}

.remove-item:hover {
    color: black;
}

.todo-item-left {
    /* // later */
    display: flex;
    align-items: center;
}

.todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
}

.todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;

    /* &:focus {
        outline: none;
    } */
}

.todo-item-edit:focus {
    outline: none;
}

.completed {
    text-decoration: line-through;
    color: grey;
}

.extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
}

button {
    font-size: 15px;
    background-color: white;
    appearance: none;

    /* &:hover {
        background: lightgreen;
    }

    &:focus {
        outline: none;
    } */
}

button:hover {
    background: greenyellow;
}

button:focus {
    outline: none;
}

.active {
    background: greenyellow;
}

/* // CSS Transitions */
.fade-enter-active,
.fade-leave-active {
    transition: opacity .2s;
}

.fade-enter,
.fade-leave-to {
    opacity: 0;
}













/* .list-container {
    display: grid;
    grid-template-columns: repeat(1fr, 3);
} */

.blueColor {
    color: darkblue;
}

.redColor {
    color: red;
}

.main-card {
    width: 100%;
}

.delete-icon {
    float: inline-start;
    cursor: pointer;
}

.card-img {
    width: 100%;
    max-height: 300px;
}

.card-header {
    justify-content: space-evenly;
}

/* New CSS Included Here  */
@media only screen and (max-width: 1000px) {
    .todo-item {
        grid-template-columns: repeat(2, 1fr);
    }
}


@media only screen and (max-width: 720px) {
    .todo-input {
        width: 80%;
    }
}
</style>
  