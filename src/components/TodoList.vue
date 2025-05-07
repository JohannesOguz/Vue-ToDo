<template>
  <div>
    <h2>Todo-lista</h2>
    <input v-model="newTodo" placeholder="Ny todo" />
    <button @click="addTodo">Lägg till</button>

    <ul>
      <li v-for="todo in todos" :key="todo.id">
        <input type="checkbox" v-model="todo.done" @change="toggleTodo(todo)" />
        <span :style="{ textDecoration: todo.done ? 'line-through' : 'none' }">
          {{ todo.text }}
        </span>
        <button @click="deleteTodo(todo)">Ta bort</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      todos: [],
      newTodo: "",
    };
  },
  mounted() {
    this.fetchTodos();
  },
  methods: {
    async fetchTodos() {
      const res = await fetch("http://localhost:3001/todos");
      this.todos = await res.json();
    },
    async addTodo() {
      if (!this.newTodo.trim) return;

      const res = await fetch("http://localhost:3001/todos", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ text: this.newTodo, done: false }),
      });
      const newTodo = await res.json();
      this.todos.push(newTodo);
      this.newTodo = "";
    },
    async toggleTodo(todo) {
      await fetch(`http://localhost:3001/todos/${todo.id}`, {
        method: "PUT",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(todo),
      });
    },
    async deleteTodo(todo) {
      await fetch(`http://localhost:3001/todos/${todo.id}`, {
        method: "DELETE",
      });
      this.todos = this.todos.filter((t) => t.id !== todo.id);
    },
  },
};
</script>
