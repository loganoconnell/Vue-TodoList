<template>
    <div id="app" class="container-fluid">
        <div class="page-header no-pageheader-top">
            <h1>Logan's <small>To-Do List</small></h1>
        </div>
        <div class="panel panel-default">
            <div class="panel-heading">
                <h3 class="panel-title"><a data-toggle="collapse" href="#addTodo">Add New To-Do</a></h3>
            </div>
            <div id="addTodo" class="panel-body collapse">
                <form id="todoForm" class="form-inline" v-on:submit.prevent="addTodo">
                    <div class="form-group">
                        <label for="todoText">Text:</label>
                        <input type="text" id="todoText" class="form-control" v-model="newTodo.text">
                    </div>
                    <div class="form-group">
                        <label for="todoDue">Due:</label>
                        <input type="text" id="todoDue" class="form-control" v-model="newTodo.due">
                    </div>
                    <div class="form-group">
                        <label for="todoList">List:</label>
                        <select id="todoList" class="form-control" v-model="newTodo.list">
                            <option v-for="list in lists" :value="list">{{list.name}}</option>
                        </select>
                    </div>
                    <input type="submit" class="btn btn-primary" value="Add">
                </form>
            </div>
        </div>
        <div class="panel panel-default">
            <div class="panel-heading">
                <h3 class="panel-title"><a data-toggle="collapse" href="#addList">Add New List</a></h3>
            </div>
            <div class="panel-body collapse" id="addList">
                <form id="listForm" class="form-inline" v-on:submit.prevent="addList">
                    <div class="form-group">
                        <label for="listName">Name:</label>
                        <input type="text" id="listName" class="form-control" v-model="newList.name">
                    </div>
                    <input type="submit" class="btn btn-primary" value="Add">
                </form>
            </div>
        </div>
        <div class="panel panel-default">
            <div class="panel-heading no-tabs-bottom">
                <h3 class="panel-title">Lists</h3>
                <ul class="nav nav-tabs nav-justified">
                    <li v-for="(list, index) in lists"><a data-toggle="tab" :href="list.name" v-on:click="setActiveNav(index)">{{list.name}}&nbsp;
                        <span class="glyphicon glyphicon-remove click closeList hidden-at-first" aria-hidden="true" v-on:click="removeList(list)"></span>
                    </a></li>
                </ul>
            </div>
            <div id="tables" class="panel-body tab-content">
                <div class="table-responsive tab-pane fade" v-for="list in lists" :id="list.name">
                    <table class="table table-bordered table-hover no-table-bottom">
                        <thead>
                            <tr>
                                <th>To-Do</th>
                                <th>Due</th>
                                <th></th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(todo, index) in list.todos" v-if="index != 0">
                                <td class="data">{{todo.text}}</td>
                                <td class="data">{{todo.due}}</td>
                                <td>
                                    <span class="glyphicon glyphicon-trash click" aria-hidden="true" v-on:click="removeTodo(list, index)"></span>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
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

    let listsRef = db.ref('lists')

    let previousNavIndex = 0;

    export default {
        name: 'app',
        firebase: {
            lists: listsRef,
        },
        data() {
            return {
                newTodo: {
                    text: '',
                    due: '',
                    list: ''
                },
                newList: {
                    name: ''
                }
            }
        },
        methods: {
            setActiveNav: function(nav) {
                $('#tables').children().eq(previousNavIndex).removeClass('in active');
                $('.closeList').eq(previousNavIndex).hide();
                $('#tables').children().eq(nav).addClass('in active');
                $('.closeList').eq(nav).show();
                previousNavIndex = nav;
            },
            addList: function() {
                listsRef.push(this.newList);
                
                this.newList.name = '';
            },
            addTodo: function() {
                let todosRef = listsRef.child(this.newTodo.list['.key']).child('todos');
                delete this.newTodo.list;
                todosRef.push(this.newTodo);

                this.newTodo.text = '';
                this.newTodo.due = '';
            },
            removeList: function(list) {
                listsRef.child(list['.key']).remove();
            },
            removeTodo: function(list, index) {
                listsRef.child(list['.key']).child('todos').child(index).remove();
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

    .data {text-align: left; }

    .click { cursor: pointer; }

    .no-pageheader-top { margin-top: 0px; }

    .no-tabs-bottom { padding-bottom: 0px; }

    .hidden-at-first { display: none; }

    .no-table-bottom { margin-bottom: 0px; }
</style>