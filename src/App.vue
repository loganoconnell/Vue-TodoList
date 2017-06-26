<template>
    <div id="app" class="container-fluid">
        <div class="page-header">
            <h1>Logan's <small>To-Do List</small></h1>
        </div>
        <div class="panel panel-default">
            <div class="panel-heading">
                <h3 class="panel-title"><a data-toggle="collapse" href="#addBody">Add New To-Do</a></h3>
            </div>
            <div class="panel-body collapse" id="addBody">
                <form id="form" class="form-inline" v-on:submit.prevent="addTodo">
                    <div class="form-group">
                        <label for="todoText">To-Do:</label>
                        <input type="text" id="todoText" class="form-control" v-model="newTodo.text">
                    </div>
                    <div class="form-group">
                        <label for="todoDue">Due:</label>
                        <input type="text" id="todoDue" class="form-control" v-model="newTodo.due">
                    </div>
                    <div class="form-group">
                        <label for="todoCategory">Category:</label>
                        <input type="text" id="todoCategory" class="form-control" v-model="newTodo.category">
                    </div>
                    <input type="submit" class="btn btn-primary" value="Add">
                </form>
            </div>
        </div>
        <div class="panel panel-default">
            <div class="panel-heading">
                <h3 class="panel-title">To-Do List</h3>
            </div>
            <div class="panel-body">
                <table class="table table-bordered table-hover">
                    <thead>
                        <tr>
                            <th>To-Do</th>
                            <th>Due</th>
                            <th>Category</th>
                            <th></th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="todo in todos">
                            <td class="data">{{todo.text}}</td>
                            <td class="data">{{todo.due}}</td>
                            <td class="data">{{todo.category}}</td>
                            <td>
                                <span class="glyphicon glyphicon-trash click" aria-hidden="true" v-on:click="removeTodo(todo)"></span>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<script>
    import Firebase from 'firebase'

    let config = {
        apiKey: "AIzaSyBZGmvu_vN-nx2isRN40WFJAWUQtsxaXks",
        authDomain: "todo-list-cd703.firebaseapp.com",
        databaseURL: "https://todo-list-cd703.firebaseio.com",
        projectId: "todo-list-cd703",
        storageBucket: "todo-list-cd703.appspot.com",
        messagingSenderId: "836846516165"
    }

    let app = Firebase.initializeApp(config)
    let db = app.database()

    let todosRef = db.ref('todos')

    export default {
        name: 'app',
        firebase: {
            todos: todosRef
        },
        data() {
            return {
                newTodo: {
                    text: '',
                    due: '',
                    category: ''
                }
            }
        },
        methods: {
            addTodo: function() {
                todosRef.push(this.newTodo);
                this.newTodo.text = '';
                this.newTodo.due = '';
                this.newTodo.category = '';
            },
            removeTodo: function(todo) {
                todosRef.child(todo['.key']).remove()
            }
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }

    .data {
        text-align: left;
    }

    .click {
        cursor: pointer;
    }
</style>