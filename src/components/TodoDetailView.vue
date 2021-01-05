<template>
  <v-dialog v-model="dialog" max-width="auto">
    <div>
      <span>{{ this.detailTodos }}</span>
    </div>
  </v-dialog>
</template>
<script>
export default {
  data() {
    return {
      detailTodos: {},
      dialog: false,
    }
  },
  watch: {
    detailData() {
      this.dialog = true;
      this.detailTodos=this.detailData
      this.open();
    }
  },
}
</script>