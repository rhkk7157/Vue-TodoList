<template>
  <v-dialog v-model="dialog" max-width="500">
   <v-card fluid class="pa-4 mt-2">
     <v-toolbar dark color="orange">
       <v-btn @click="dialog = false" icon>
         <v-icon>mdi-close</v-icon>
       </v-btn>
     <v-card-title>TodoList View</v-card-title>
     </v-toolbar>
     <br />
     <v-form>
       <v-text-field
          v-model="detailTodo.title"
          label="제목"
          prepend-icon="mdi-human"
          type="text"
          outlined
          :disabled="detailTodo.completed"
        ></v-text-field>
        <v-text-field
          v-model="detailTodo.content"
          label="내용"
          prepend-icon="mdi-comment-text-outline"
          type="text"
          outlined
          :disabled="detailTodo.completed"
        ></v-text-field>
        <v-text-field
          v-model="detailTodo.deadline"
          label="마감기한"
          prepend-icon="mdi-calendar"
          type="text"
          outlined
          readonly
          :disabled="detailTodo.completed"
        ></v-text-field>
        <v-card-actions>
          <v-btn @click="onClickUpdate(detailTodo.title, detailTodo.content, detailTodo.completed, detailTodo.index)" color="default" block dark>수정</v-btn>
        </v-card-actions>
     </v-form>
   </v-card>
  </v-dialog>
</template>
<script>
export default {
  name: 'TodoDetailComponent',
  data() {
    return {
      detailTodo: {},
      dialog: false,
    }
  },
  methods: {
    open(updateData) {
      this.dialog = true;
      this.detailTodo = updateData;
    },
    close() {
      this.dialog = false;
      this.$emit('onClosed');
    },
    cancel() {
      this.dialog = false;
    },
    onClickUpdate(updatedTitle, updatedContent, completed, index) {
      if(completed) {
        alert('완료체크 된 항목은 수정 할 수 없습니다.');
      }
      this.$emit('updatedData', updatedTitle, updatedContent, index);
      this.cancel();
    }
  },
}
</script>