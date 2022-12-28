<template>
  <input
    type="text"
    autofocus="autofocus"
    autocomplete="off"
    placeholder="What needs to be done?"
    class="new-todo"
    v-model="newTodo"
    @keyup.enter="submit"
  />
</template>

<script>
export default {
  data() {
    return {
      newTodo: ''
    }
  },
  methods: {
    reset() {
      this.newTodo = ''
    },
    submit() {
      this.$emit('submit', this.newTodo)
      this.reset()
    }
  }
}
</script>

<style lang="scss" scoped>
.new-todo {
  padding: 16px 16px 16px 60px;
  border: none;
  background: rgba(0, 0, 0, 0.003);
  box-shadow: inset 0 -2px 1px rgba(0, 0, 0, 0.03);
}
</style>
