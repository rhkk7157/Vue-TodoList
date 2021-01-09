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
                    <v-toolbar-title class="headline">Todo List</v-toolbar-title>
                    <v-spacer></v-spacer>
                  </v-toolbar>
                  <v-list two-line subheader>
                    <br />
                    <p class="mx-12 text-right">총 <b>{{todos.length}}</b> 개</p>
                    <v-form>
                      <v-text-field 
                        v-model="newTodoTitle" 
                        label="제목"  
                        prepend-icon="mdi-human"
                        type="text"
                        outlined
                      ></v-text-field>
                      <v-text-field 
                        v-model="newTodoContent" 
                        label="내용"  
                        prepend-icon="mdi-comment-text-outline"
                        type="text"
                        outlined
                        @keyup.enter="addTodo"
                      ></v-text-field>
                      <v-text-field
                        v-if="newTodoTitle !== ''" 
                        v-model="picker" 
                        label="마감기한"  
                        prepend-icon="mdi-calendar"
                        outlined
                        readonly
                      >
                      </v-text-field>
                      <v-date-picker 
                        v-if="newTodoTitle !== '' || this.picker !== null"
                        color="yellow darken-4" 
                        v-model="picker"
                        ></v-date-picker>
                      <v-card-actions>
                        <v-btn @click="addTodo()" color="default" block dark>Add</v-btn>
                      </v-card-actions>
                    </v-form>
                  </v-list>

                  <v-list subheader two-line flat>
                    <v-subheader class="subheading" v-if="todos.length == 1">Your Tasks</v-subheader>
                    <v-list-item-group>
                      <v-list-item v-for="(todo,i) in filterTodos" :key="todo.id">
                        <template #default="{ }">
                          <v-list-item-action>
                            <v-checkbox class="checkbox-1" v-on:change="onClickCompleted(todo)" v-bind:checked="todo.completed"></v-checkbox>
                          </v-list-item-action>
                          <v-list-item-content @click="onClickUpdateTodo(todo.title, todo.content, todo.completed, todo.deadline, i)">
                            <v-list-item-title 
                                  v-if="todo.completed" 
                                  v-bind:style="{ textDecoration: textDecoration }"
                              >{{ todo.title }}</v-list-item-title>
                            <v-list-item-title v-else>{{ todo.title }}</v-list-item-title>
                            <v-btn fab ripple small color="white" v-if="active" @click="onClickRemove(i)">
                              <v-icon class="black--text">mdi-delete</v-icon>
                            </v-btn>
                          </v-list-item-content>
                           <v-btn fab ripple small color="white" v-if="active" @click="onClickRemove(i)">
                            <v-icon class="black--text">mdi-delete</v-icon>
                          </v-btn>
                          <v-btn fab ripple small color="red" v-if="todo.notification === 1">
                            <v-icon class="white--text">mdi-bell-ring</v-icon>
                          </v-btn>
                          <v-btn fab ripple small color="white" @click="onClickRemove(i)">
                            <v-icon class="black--text">mdi-delete</v-icon>
                          </v-btn>
                        </template>
                      </v-list-item>
                    </v-list-item-group>
                    <!-- <v-btn @click="allClear">전체삭제</v-btn> -->
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
      console.log(this.todos);
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
    onClickUpdateTodo(title, content, completed, deadline, i) {
      if(completed) {
        alert('완료 항목은 수정 할 수 없습니다.');
        return false;
      }
      this.updateTodo.index = i;
      this.updateTodo.title = title;
      this.updateTodo.content = content;
      this.updateTodo.deadline = deadline;
      this.$refs.TodoDetailComponent.open(this.updateTodo);
    },
    allClear() {
      this.todos = [];
    },
    newUpdated(title, content, index) {
      this.todos[index].title = title;
      this.todos[index].content = content;
    },
  },
}
</script>
<style scoped>
</style>