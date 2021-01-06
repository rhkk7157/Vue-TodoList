<template>
  <div id="app">
    <v-app>
      <v-main>
        <v-theme-provider>
          <v-container>
            <v-row justify="center" class="ma-5">
              <v-col xs="12" sm="8">
                <v-card>
                  <v-toolbar color="yellow darken-4" dark>
                    <v-toolbar-side-icon></v-toolbar-side-icon>
                    <v-toolbar-title class="headline">Todo List</v-toolbar-title>
                    <v-spacer></v-spacer>
                
                  </v-toolbar>
                  <v-list two-line subheader>
                    <!-- <v-subheader class="headline">{{day}}, {{date}}{{ord}} {{year}}</v-subheader> -->
                    <br />
                    <p class="mx-12 text-right">총 <b>{{todos.length}}</b> 개</p>
                    <v-list-item>
                      <v-list-item-content>
                        <v-list-item-title>
                          <v-text-field v-model="newTodoTitle" name="newTodoTitle" label="제목"/>
                          <v-text-field v-model="newTodoContent" name="newTodoContent" label="내용" @keyup.enter="addTodo"/>
                          <div v-if="newTodoTitle !== ''">
                            <p>마감기한</p>
                            <v-date-picker v-model="deadline"></v-date-picker>
                          </div>
                          <div>
                            <v-btn @click="addTodo">Add</v-btn>
                          </div>
                          <br/>
                          <br/>
                        </v-list-item-title>
                      </v-list-item-content>
                    </v-list-item>
                  </v-list>

                  <v-list subheader two-line flat>
                    <v-subheader class="subheading" v-if="todos.length == 1">Your Tasks</v-subheader>
                    <v-list-item-group>
                      <v-list-item v-for="(todo, i) in filterTodos" :key="todo.id">
                        <template #default="{ active }">
                          <v-list-item-action>
                            <v-checkbox class="checkbox-1" v-on:change="onClickCompleted(todo)" v-bind:checked="todo.completed"></v-checkbox>
                          </v-list-item-action>
                          <v-list-item-content>
                            <v-list-item-title 
                                  v-on:keydown.enter="updateTask($event, todo)" 
                                  v-on:blur="updateTask($event, todo)" 
                                  v-bind:class="{completed: todo.completed}" 
                                  v-if="todo.completed" 
                                  v-bind:style="{ textDecoration: textDecoration }"
                              >{{ todo.title | capitalize }}</v-list-item-title>
                            <v-list-item-title v-else>{{ todo.title | capitalize }}</v-list-item-title>
                          </v-list-item-content>
                          <v-btn fab ripple small color="red" v-if="todo.notification === 1">
                            <v-icon class="white--text">mdi-bell-ring</v-icon>
                          </v-btn>
                          <!-- <v-btn fab ripple small color="pink" v-if="active" @click="updatedTodo(i)">
                            <v-icon class="white--text">mdi-pencil-outline</v-icon>
                          </v-btn> -->
                          <v-btn fab ripple small color="white" v-if="active" @click="onClickRemove(i)">
                            <v-icon class="black--text">mdi-delete</v-icon>
                          </v-btn>
                           <!-- <v-btn fab ripple small color="green" v-if="active" @click="onClickCompleted(i)">
                            <v-icon class="white--text">mdi-check</v-icon>
                          </v-btn>  -->
                        </template>
                      </v-list-item>
                      <v-btn @click="allClear">전체삭제</v-btn>
                    </v-list-item-group>
                  </v-list>
                </v-card>
              </v-col>
            </v-row>
          </v-container>
        </v-theme-provider>
      </v-main>
    </v-app>
    <todo-detail-view v-if="active" v-bind:detailData="this.todos"></todo-detail-view>
  </div>
</template>
<script>
import { DateTime } from 'luxon';
import TodoDetailView from './TodoDetailView';
import _ from 'lodash';

export default {
  components: {
    TodoDetailView
  },
  data () {
    return {
      show: true,
      newTodoTitle: '',  // 할일 제목
      newTodoContent: '', //할일 내용
      todos: [],
      active: false,
      deadline: new Date().toISOString().substr(0, 10),
      completed: false,
      textDecoration: 'line-through',
    }
  },
  computed: {
    filterTodos(){
      return _.sortBy(this.todos, ['completed', false])
    }
  },
  methods: {
    addTodo() {
      const title = this.newTodoTitle && this.newTodoTitle.trim();
      const deadlineDate = this.deadline.replace(/-/gi,'');
      const currentDate = DateTime.local().toFormat('yyyyLLdd');
      let notification = 0;

      if (!title) {
        alert('제목을 입력해주세요');
        return;
      }
      if (currentDate - deadlineDate > 0) {
        notification = 1;
      }

      this.todos.push({
        id: this.todos.length+1,
        title: this.newTodoTitle,
        content: this.newTodoContent,
        deadline: this.deadline,
        currentDate: currentDate,
        notification: notification,
        completed: this.completed,
        done: false
      });
      this.newTodoTitle = '';
      this.newTodoContent = '';
    },
    onClickRemove(index) {
      this.todos.splice(index, 1);
    },
    updatedTodo(index) {
      console.log(index, 'updated');
    },
    onClickCompleted(todo) {
      todo.completed = !todo.completed;
      // const getCompleted = this.todos[index].completed;
      // this.todos[index].completed = !getCompleted;
      // console.log(this.todos[index].completed);
    },
    allClear() {
      this.todos = [];
    },
    updateTask(e, todo) {
      e.preventDefault();
      todo.title = e.target.innerText;
      e.target.blur();
    },
  },
  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  }
}
</script>
<style scoped>
/* .done {
  text-decoration: line-through;
} */
</style>