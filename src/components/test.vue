<template>
    <div id="todoApp">
    <h3> {{message}} </h3>
    <v-form @submit.prevent="addTask">
      <v-container>
        <v-row>
          <v-col cols="12" md="4">
            <v-text-field v-model="addTodoInput" label="제목"></v-text-field>
            <v-text-field v-model="addTodoContent" label="내용"></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
            <v-btn type="submit">Add</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
    
    <div class="todo-lists" v-if="lists.length">
      <h3>My Todo Tasks</h3>
      <ul>
        <li v-for="list in filterLists" :key="list.id">
          <input type="checkbox" v-on:change="completeTask(list)" v-bind:checked="list.isComplete"/>
          <span class="title" contenteditable="true" v-on:keydown.enter="updateTask($event, list)" v-on:blur="updateTask($event, list)" v-bind:class="{completed: list.isComplete}">{{list.title}}</span>
          <span class="remove" v-on:click="removeTask(list)">x</span>
        </li>
      </ul>
    </div>
  </div>
</template>
<script src="https://unpkg.com/vue"></script>
<script>
import _ from 'lodash'
export default {
  data() {
    return {
    message: 'TODO LIST',
    addTodoInput: '',
    addTodoContent: '',
    lists: [],
    hasError: false
    }
  },
  computed: {
    filterLists(){
      console.log('--------------------computed');
      return _.sortBy(this.lists, ['isComplete', false])
    }
  },
  methods:{
    addTask() {
      if(!this.addTodoInput){
        this.hasError = true;
        return;
      }
      
      this.hasError = false;
      this.lists.push({
        id:this.lists.length+1, 
        title: this.addTodoInput,
        content: this.addTodoContent,
        isComplete: false
      });
      
      this.addTodoInput = '';
    },
    
    updateTask: function(e, list){
      e.preventDefault();
      list.title = e.target.innerText;
      e.target.blur();
    },
    
    completeTask: function(list){
      console.log(list);
      list.isComplete = !list.isComplete;
    },
    
    removeTask: function(list){
      var index = _.findIndex(this.lists, list);
      this.lists.splice(index, 1);
    }
    
  }
};

//generate dummy data for demo purpose

</script>

