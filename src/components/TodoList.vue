<template>
  <div id="app">
    <v-app>
      <v-main>
        <v-theme-provider>
          <v-container>
            <v-row justify="center" class="ma-5">
              <v-col xs="12" sm="6">
                <v-card>
                  <v-toolbar color="yellow darken-4" dark>
                    <v-toolbar-title class="headline">Todo List</v-toolbar-title>
                    <v-spacer></v-spacer>
                  </v-toolbar>
                  <v-list two-line subheader>
                    <br />
                    <p class="mx-12 text-right">총 <b>{{todos.length}}</b> 개</p>
                    <v-form>
                      <v-col cols="12" sm="12">
                        <v-text-field 
                          v-model="newTodoTitle" 
                          label="제목"  
                          prepend-icon="mdi-human"
                          type="text"
                          outlined
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="12">
                        <v-text-field 
                          v-model="newTodoContent" 
                          label="내용"  
                          prepend-icon="mdi-comment-text-outline"
                          type="text"
                          outlined
                          @keyup.enter="addTodo"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="12">
                        <v-text-field
                          v-if="newTodoTitle !== ''" 
                          v-model="picker" 
                          label="마감기한"  
                          prepend-icon="mdi-calendar"
                          outlined
                          readonly
                        >
                        </v-text-field>
                      </v-col>
                      <v-row justify="center">
                        <v-date-picker 
                          v-if="newTodoTitle !== '' || this.picker !== null"
                          color="yellow darken-4" 
                          v-model="picker"
                          style="padding-left:10px"
                        ></v-date-picker>
                      </v-row>
                      <v-row justify="center">
                        <v-col cols="12" sm="12">
                          <v-card-actions>
                            <v-btn @click="addTodo()" color="default" block dark>Add</v-btn>
                          </v-card-actions>
                        </v-col>
                      </v-row>
                    </v-form>
                  </v-list>
                  <br />
                  <v-list subheader two-line flat>
                    <v-row  v-if="todos.length !== 0">
                      <v-card-actions cols="12" md="6" style="display:flex;position:absolute;right:0">
                        <v-btn @click="allClear" depressed>All Clear</v-btn>
                      </v-card-actions>
                      <v-subheader class="subheading">Your Tasks</v-subheader>
                    </v-row>
                    <br />
                    <v-list-item-group>
                      <v-list-item v-for="todo in filterTodos" :key="todo.id">
                        <template>
                          <v-list-item-action>
                            <v-checkbox class="checkbox-1" v-on:change="onClickCompleted(todo)" v-bind:checked="todo.completed"></v-checkbox>
                          </v-list-item-action>
                          <v-list-item-content @click="onClickUpdateTodo(todo.title, todo.content, todo.completed, todo.deadline, todo.currentDate, todo.id)">
                            <v-list-item-title 
                              v-if="todo.completed" 
                              v-bind:style="{ textDecoration: textDecoration }"
                            >{{ todo.title }}</v-list-item-title>
                            <v-list-item-title v-else>{{ todo.title }}</v-list-item-title>
                          </v-list-item-content>
                          <v-btn fab ripple small color="red" v-if="todo.notification === 1">
                            <v-icon class="white--text">mdi-bell-ring</v-icon>
                          </v-btn>
                          <v-btn fab ripple small color="white" @click="onClickRemove(i)">
                            <v-icon class="black--text">mdi-delete</v-icon>
                          </v-btn>
                        </template>
                      </v-list-item>
                    </v-list-item-group>
                  </v-list>
                </v-card>
              </v-col>
            </v-row>
            <TodoDetailComponent ref="TodoDetailComponent" @onClosed="cancel()" v-on:updatedData="newUpdated"></TodoDetailComponent>
          </v-container>
        </v-theme-provider>
      </v-main>
    </v-app>
  </div>
</template>
<script>
import { DateTime } from 'luxon';
import TodoDetailComponent from '@/components/TodoDetailComponent';
import _ from 'lodash';

export default {
  name: 'TodoList',
  components: {
    TodoDetailComponent,
  },
  data () {
    return {
      newTodoTitle: '',  // 할일 제목
      newTodoContent: '', //할일 내용
      todos: [],
      active: false,
      picker: null,
      deadline: new Date().toISOString().substr(0, 10),
      completed: false,
      textDecoration: 'line-through',
      updateTodo: {},
    }
  },
  computed: {
    filterTodos() {
      return _.sortBy(this.todos, ['completed', false])
    }
  },
  methods: {
    replaceDate(value) {
      if(!value) {
        return;
      }
      return value.replace(/-/gi,'');
    },
    addTodo() {
      const title = this.newTodoTitle && this.newTodoTitle.trim();
      const deadlineDate = this.replaceDate(this.picker);
      const getCurrentDate = DateTime.local().toFormat('yyyy-LL-dd');
      const replacedCurrentDate = this.replaceDate(getCurrentDate);
      let notification = 0;


      if (!title) {
        alert('제목을 입력해주세요');
        return;
      } else if(!deadlineDate) {
        alert('마감기한을 입력해주세요');
        return;
      }

      if (replacedCurrentDate - deadlineDate > 0) {
        notification = 1;
      }
      if (this.picker === null) {
        this.picker = getCurrentDate;
      }

      this.todos.push({
        id: this.todos.length+1,
        title: this.newTodoTitle,
        content: this.newTodoContent,
        deadline: this.picker,
        currentDate: getCurrentDate,
        notification: notification,
        completed: this.completed,
      });
      this.newTodoTitle = '';
      this.newTodoContent = '';
      this.picker = null;
    },
    onClickRemove(index) {
      this.todos.splice(index, 1);
    },
    onClickCompleted(todo) {
      todo.completed = !todo.completed;
    },
    onClickUpdateTodo(title, content, completed, deadline, currentDate, i) {
      this.updateTodo.index = i;
      this.updateTodo.title = title;
      this.updateTodo.content = content;
      this.updateTodo.deadline = deadline;
      this.updateTodo.completed = completed;
      this.$refs.TodoDetailComponent.open(this.updateTodo);
    },
    allClear() {
      this.todos = [];
    },
    newUpdated(title, content, index) {
      this.todos[index-1].title = title;
      this.todos[index-1].content = content;
    },
  },
}
</script>
<style scoped>
</style>