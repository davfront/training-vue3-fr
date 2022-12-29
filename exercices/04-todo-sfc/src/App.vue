<template>
  <TodoLayout>
    <!-- header -->
    <template #header>
      <h1>todos</h1>
      <TodoNewForm @submit="addTodo($event)" />
    </template>

    <!-- body -->
    <template #default v-if="hasTodo">
      <TodoToggleSwitch
        :checked="activeCount === 0"
        @click="toggleAllTodos()"
      />
      <TodoList
        :todos="filteredTodos"
        @remove-request="removeTodo($event)"
        @update-request="updateTodo($event)"
      />
    </template>

    <!-- footer -->
    <template #footer v-if="hasTodo">
      <span class="todo-count">
        <strong>{{ activeCount }}</strong>
        {{ activeCount > 1 ? 'items left' : 'item left' }}
      </span>
      <TodoFilter v-model="filter" />
      <button class="clear-completed" @click="removeCompletedTodos()">
        Clear completed
      </button>
    </template>

    <!-- info -->
    <template #info>
      <p>Double-click to edit a todo</p>
    </template>
  </TodoLayout>
</template>

<script>
import TodoLayout from '@/components/TodoLayout.vue'
import TodoNewForm from '@/components/TodoNewForm.vue'
import TodoList from '@/components/TodoList.vue'
import TodoToggleSwitch from '@/components/TodoToggleSwitch.vue'
import TodoFilter from '@/components/TodoFilter.vue'

export default {
  components: {
    TodoLayout,
    TodoNewForm,
    TodoList,
    TodoToggleSwitch,
    TodoFilter
  },
  data() {
    return {
      todos: JSON.parse(localStorage.getItem('todos')) || [],
      filter: 'all'
    }
  },
  computed: {
    maxId() {
      return this.todos.reduce(
        (maxId, todo) => Math.max(maxId, todo.id),
        this.todos[0]?.id || 0
      )
    },
    activeTodos() {
      return this.todos.filter((todo) => !todo.completed)
    },
    activeCount() {
      return this.activeTodos.length
    },
    completedTodos() {
      return this.todos.filter((todo) => todo.completed)
    },
    hasTodo() {
      return this.todos.length > 0
    },
    filteredTodos() {
      if (this.filter === 'active') {
        return this.activeTodos
      }
      if (this.filter === 'completed') {
        return this.completedTodos
      }
      return this.todos
    }
  },
  methods: {
    addTodo(label) {
      label = label.split(' ').filter(Boolean).join(' ')
      if (label) {
        this.todos.push({
          id: this.maxId + 1,
          label: label,
          completed: false
        })
      }
    },
    removeTodo(todo) {
      const index = this.todos.findIndex((t) => t.id === todo.id)
      if (index > -1) {
        this.todos.splice(index, 1)
      }
    },
    removeCompletedTodos() {
      this.completedTodos.forEach(this.removeTodo)
    },
    updateTodo(newTodo) {
      const index = this.todos.findIndex((todo) => todo.id === newTodo.id)
      if (index > -1) {
        this.todos[index].label = newTodo.label
        this.todos[index].completed = newTodo.completed
      }
    },
    toggleAllTodos() {
      if (this.activeCount > 0) {
        this.activeTodos.forEach((todo) => (todo.completed = true))
      } else {
        this.completedTodos.forEach((todo) => (todo.completed = false))
      }
    }
  },
  watch: {
    todos: {
      deep: true,
      handler(todos) {
        localStorage.setItem('todos', JSON.stringify(todos))
      }
    }
  }
}
</script>
