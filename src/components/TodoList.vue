<template>
  <div id="app">
    <v-app>
      <v-main>
        <v-theme-provider root :dark="isDark">
          <v-container>
            <v-row justify="center" class="ma-5">
              <v-col xs="12" sm="8">
                <v-card>
                  <v-toolbar color="yellow darken-4" dark>
                    <v-toolbar-side-icon></v-toolbar-side-icon>
                    <v-toolbar-title class="headline">Todo List</v-toolbar-title>
                    <v-spacer></v-spacer>
                    <!-- <v-btn icon> -->
                      <!-- <v-icon>mdi-magnify</v-icon> -->
                    <!-- </v-btn> -->
                    <v-tooltip bottom>
                      <template v-slot:activator="{ on }">
                        <v-btn icon @click="isDark = !isDark" v-on="on">
                          <v-icon v-model="isDark">{{ !isDark ? 'mdi-weather-night' : 'mdi-weather-cloudy' }}</v-icon>
                        </v-btn>
                      </template>
                      <span>
                        {{ isDark ? 'light mode' : 'dark mode' }}
                      </span>
                    </v-tooltip>
                  </v-toolbar>
                  <v-list two-line subheader>
                    <!-- <v-subheader class="headline">{{day}}, {{date}}{{ord}} {{year}}</v-subheader> -->
                    <br />
                    <p class="mx-12 text-right">총 <b>{{todos.length}}</b> 개</p>
                    <v-list-item>
                      <v-list-item-content>
                        <v-list-item-title>
                          <v-text-field v-model="newTodoTitle" name="newTodoTitle" label="Title"/>
                          <v-text-field v-model="newTodoContent" name="newTodoContent" label="Content" @keyup.enter="addTodo"/>
                          <div v-if="newTodoTitle !== ''">
                              <v-date-picker v-model="deadline"></v-date-picker>
                          </div>
                          <v-btn @click="addTodo">Save</v-btn>
                        </v-list-item-title>
                      </v-list-item-content>
                               
                    </v-list-item>
                  </v-list>

                  <v-list subheader two-line flat>
                    <!-- <v-subheader class="subheading" v-if="todos.length == 0">You have 0 Tasks, add some</v-subheader> -->
                    <v-subheader class="subheading" v-if="todos.length == 1">Your Tasks</v-subheader>

                    <v-list-item-group>
                      <v-list-item v-for="(todo, i) in todos" :key="i">
                        <template #default="{ active, toggle }">
                          <v-list-item-action>
                            <v-checkbox @click="toggle"></v-checkbox>
                          </v-list-item-action>
                          <v-list-item-content>
                            <v-list-item-title :class="{
                    done: active
                    }">{{ todo.title | capitalize }}</v-list-item-title>
                            <v-list-item-content>
                              <v-list-item-title>내용: {{todo.content}}</v-list-item-title>
                              <v-list-item-title>마감기한: {{todo.deadline}}</v-list-item-title>
                            </v-list-item-content>
                          </v-list-item-content>
                          <v-btn fab ripple small color="red" v-if="todo.notification === 1">
                            <v-icon class="white--text">mdi-bell</v-icon>
                          </v-btn>
                          <v-btn fab ripple small color="pink" v-if="active" @click="updatedTodo(i)">
                            <v-icon class="white--text">mdi-pencil-outline</v-icon>
                          </v-btn>
                          <!-- <v-btn fab ripple small color="green" v-if="active" @click="completed(i)">
                            <v-icon class="white--text">mdi-check</v-icon>
                          </v-btn>
                          <v-btn fab ripple small color="red" v-if="active" @click="removeTodo(i)">
                            <v-icon class="white--text">mdi-trash-can-outline</v-icon>
                          </v-btn> -->
                        </template>
                      </v-list-item>
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

export default {
  components: {
    TodoDetailView
  },
  data () {
    return {
      isDark:false,
      show: true,
      newTodoTitle: '',  // 할일 제목
      newTodoContent: '', //할일 내용
      todos: [],
      day: this.todoDay(),
      date: new Date().getDate(),
      ord: this.nth(new Date().getDate()),
      year: new Date().getFullYear(),
      active: false,
      deadline: new Date().toISOString().substr(0, 10),
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
        title: this.newTodoTitle,
        content: this.newTodoContent,
        deadline: this.deadline,
        currentDate: currentDate,
        notification: notification,
        done: false
      });
      this.newTodoTitle = '';
      this.newTodoContent = '';
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
    },
    updatedTodo(index) {
      console.log(index);
    },
    openModal(index) {
      console.log(index);
    },
    todoDay() {
      var d = new Date();
      var days = ["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"];
      return days[d.getDay()];
    },
    nth(d) {
      if(d>3 && d<21) return 'th';
      switch (d % 10) {
        case 1:  return "st";
        case 2:  return "nd";
        case 3:  return "rd";
        default: return "th";
      }
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