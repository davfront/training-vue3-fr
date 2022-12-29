<template>
  <ul class="todo-list">
    <TodoListItem
      v-for="todo in todos"
      :key="todo.id"
      :todo="todo"
      @remove-request="removeTodo(todo)"
      @update-request="updateTodo($event)"
    />
  </ul>
</template>

<script>
import TodoListItem from '@/components/TodoListItem.vue'
export default {
  components: {
    TodoListItem
  },
  props: {
    todos: {
      type: Array,
      default: () => []
    }
  },
  methods: {
    removeTodo(todo) {
      this.$emit('remove-request', todo)
    },
    updateTodo(todo) {
      this.$emit('update-request', todo)
    }
  }
}
</script>

<style lang="scss" scoped>
.todo-list {
  margin: 0;
  padding: 0;
  list-style: none;
}
</style>
