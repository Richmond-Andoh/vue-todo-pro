<template>
  <div class="container mx-auto p-6 max-w-lg">
    <h1 class="text-5xl text-white font-bold text-center mb-6">Vue Todo App Pro</h1>

    <!-- Add Todo Form -->
    <div class="mb-4">
      <input
        v-model="newTodo"
        @keyup.enter="addTodo"
        placeholder="Add a new task..."
        class="w-full p-2 border rounded"
      />
    </div>

    <!-- Filter Buttons -->
    <div class="flex justify-center space-x-2 mb-4">
      <button
        @click="filter = 'all'"
        :class="filter === 'all' ? 'bg-blue-500' : 'bg-gray-300'"
        class="p-2 rounded text-white"
      >
        All
      </button>
      <button
        @click="filter = 'completed'"
        :class="filter === 'completed' ? 'bg-blue-500' : 'bg-gray-300'"
        class="p-2 rounded text-white"
      >
        Completed
      </button>
      <button
        @click="filter = 'uncompleted'"
        :class="filter === 'uncompleted' ? 'bg-blue-500' : 'bg-gray-300'"
        class="p-2 rounded text-white"
      >
        Uncompleted
      </button>
    </div>

    <!-- Todo List -->
    <ul class="space-y-3">
      <li
        v-for="(todo, index) in filteredTodos"
        :key="index"
        class="flex justify-between items-center p-3 bg-gray-100 rounded"
      >
        <div :class="{ 'line-through text-gray-400': todo.completed }">
          <span v-if="editIndex !== index">{{ todo.text }}</span>
          <input
            v-else
            v-model="editTodoText"
            @keyup.enter="updateTodo"
            class="border rounded px-2"
          />
        </div>

        <!-- Buttons: Edit, Complete, Delete -->
        <div class="flex space-x-2">
          <button
            @click="toggleComplete(index)"
            :class="todo.completed ? 'bg-green-500' : 'bg-gray-300'"
            class="p-1 text-white rounded"
          >
            ✓
          </button>

          <button
            @click="editIndex === index ? updateTodo() : startEditing(index, todo.text)"
            class="bg-blue-500 text-white p-1 rounded"
          >
            {{ editIndex === index ? 'Save' : 'Edit' }}
          </button>

          <button @click="confirmDelete(index)" class="bg-red-500 text-white p-1 rounded">
            Delete
          </button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTodo: '',
      todos: JSON.parse(localStorage.getItem('todos')) || [],
      editIndex: null,
      editTodoText: '',
      filter: 'all' // all | completed | uncompleted
    }
  },
  computed: {
    filteredTodos() {
      if (this.filter === 'completed') {
        return this.todos.filter((todo) => todo.completed)
      } else if (this.filter === 'uncompleted') {
        return this.todos.filter((todo) => !todo.completed)
      } else {
        return this.todos
      }
    }
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim() !== '') {
        this.todos.push({ text: this.newTodo, completed: false })
        this.newTodo = ''
        this.saveTodos()
      }
    },
    deleteTodo(index) {
      this.todos.splice(index, 1)
      this.saveTodos()
    },
    confirmDelete(index) {
      const isConfirmed = confirm('Are you sure you want to delete this task?')
      if (isConfirmed) {
        this.deleteTodo(index)
      }
    },
    toggleComplete(index) {
      this.todos[index].completed = !this.todos[index].completed
      this.saveTodos()
    },
    startEditing(index, text) {
      this.editIndex = index
      this.editTodoText = text
    },
    updateTodo() {
      if (this.editTodoText.trim() !== '') {
        this.todos[this.editIndex].text = this.editTodoText
        this.editIndex = null
        this.editTodoText = ''
        this.saveTodos()
      }
    },
    saveTodos() {
      localStorage.setItem('todos', JSON.stringify(this.todos))
    }
  }
}
</script>
