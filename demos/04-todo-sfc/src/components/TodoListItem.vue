<template>
  <li
    class="todo-list-item"
    :class="{ completed: todo.completed, editing: isTodoEditing }"
    @dblclick="enableEdition()"
  >
    <div class="view">
      <input
        type="checkbox"
        class="toggle"
        :checked="todo.completed"
        @click="updateTodoState()"
      />
      <label>{{ todo.label }}</label>
      <button class="destroy" @click="removeTodo()"></button>
    </div>
    <input
      type="text"
      class="edit"
      ref="editInput"
      v-model="newLabel"
      @keyup.enter="updateTodoLabel()"
      @blur="updateTodoLabel()"
      @keyup.esc="disableEdition()"
    />
  </li>
</template>

<script>
export default {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      isTodoEditing: false,
      newLabel: ''
    }
  },
  methods: {
    enableEdition() {
      this.isTodoEditing = true
      this.newLabel = this.todo.label
      this.$nextTick(() => {
        const inputEl = this.$refs.editInput
        if (inputEl) {
          inputEl.focus()
        }
      })
    },
    removeTodo() {
      this.$emit('remove-request')
    },
    updateTodoLabel() {
      if (this.isTodoEditing) {
        let newLabel = this.newLabel
        newLabel = newLabel.split(' ').filter(Boolean).join(' ')
        if (newLabel) {
          const newTodo = {
            ...this.todo,
            label: newLabel
          }
          this.$emit('update-request', newTodo)
        } else {
          this.removeTodo()
        }
      }
      this.disableEdition()
    },
    updateTodoState() {
      this.$emit('update-request', {
        ...this.todo,
        completed: !this.todo.completed
      })
    },
    disableEdition() {
      this.isTodoEditing = false
      this.newLabel = ''
    }
  }
}
</script>

<style lang="scss" scoped>
.todo-list-item {
  position: relative;
  font-size: 24px;
  border-bottom: 1px solid #ededed;

  &:last-child {
    border-bottom: none;
  }

  &.editing {
    border-bottom: none;
    padding: 0;

    .edit {
      display: block;
      width: 506px;
      padding: 12px 16px;
      margin: 0 0 0 43px;
    }

    .view {
      display: none;
    }
  }

  .toggle {
    text-align: center;
    width: 40px;
    /* auto, since non-WebKit browsers doesn't support input styling */
    height: auto;
    position: absolute;
    top: 0;
    bottom: 0;
    margin: auto 0;
    border: none; /* Mobile Safari */
    -webkit-appearance: none;
    appearance: none;

    opacity: 0;

    & + label {
      /*
        Firefox requires `#` to be escaped - https://bugzilla.mozilla.org/show_bug.cgi?id=922433
        IE and Edge requires *everything* to be escaped to render, so we do that instead of just the `#` - https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/7157459/
      */
      background-image: url('data:image/svg+xml;utf8,%3Csvg%20xmlns%3D%22http%3A//www.w3.org/2000/svg%22%20width%3D%2240%22%20height%3D%2240%22%20viewBox%3D%22-10%20-18%20100%20135%22%3E%3Ccircle%20cx%3D%2250%22%20cy%3D%2250%22%20r%3D%2250%22%20fill%3D%22none%22%20stroke%3D%22%23ededed%22%20stroke-width%3D%223%22/%3E%3C/svg%3E');
      background-repeat: no-repeat;
      background-position: center left;
    }

    &:checked + label {
      background-image: url('data:image/svg+xml;utf8,%3Csvg%20xmlns%3D%22http%3A//www.w3.org/2000/svg%22%20width%3D%2240%22%20height%3D%2240%22%20viewBox%3D%22-10%20-18%20100%20135%22%3E%3Ccircle%20cx%3D%2250%22%20cy%3D%2250%22%20r%3D%2250%22%20fill%3D%22none%22%20stroke%3D%22%23bddad5%22%20stroke-width%3D%223%22/%3E%3Cpath%20fill%3D%22%235dc2af%22%20d%3D%22M72%2025L42%2071%2027%2056l-4%204%2020%2020%2034-52z%22/%3E%3C/svg%3E');
    }
  }

  label {
    word-break: break-all;
    padding: 15px 15px 15px 60px;
    display: block;
    line-height: 1.2;
    transition: color 0.4s;
  }

  &.completed label {
    color: #d9d9d9;
    text-decoration: line-through;
  }

  .destroy {
    display: none;
    position: absolute;
    top: 0;
    right: 10px;
    bottom: 0;
    width: 40px;
    height: 40px;
    margin: auto 0;
    font-size: 30px;
    color: #cc9a9a;
    margin-bottom: 11px;
    transition: color 0.2s ease-out;

    &:hover {
      color: #af5b5e;
    }

    &:after {
      content: '×';
    }
  }

  &:hover .destroy {
    display: block;
  }

  .edit {
    display: none;
  }

  &.editing:last-child {
    margin-bottom: -1px;
  }
}
</style>
