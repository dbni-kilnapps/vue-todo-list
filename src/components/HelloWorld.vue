<template>
  <nav>
    <a href="https://github.com/dbni-kilnapps/vue-todo-list" target="_blank">GitHub</a>
  </nav>
  <h1>My To-Do List</h1>
  <div>
    <input type="text" placeholder="Search..." v-model="searchQuery" />
    <ul>
      <!-- check box -->
      <li v-for="(todo, i) in filteredTodos" v-bind:key="i">
        <label :for="'todo-checkbox-' + i" class="todo-label">
          <input
            type="checkbox"
            v-model="todo.done"
            :id="'todo-checkbox-' + i"
          />
          <span
            v-bind:style="{
              textDecoration: todo.done ? 'line-through' : 'none',
            }"
            >{{ todo.text }}</span
          >
        </label>
        <button @click="() => removeTodo(i)">
          <span class="material-symbols-outlined"> delete </span>
        </button>
      </li>
    </ul>

    <form v-on:submit.prevent="addTodo">
      <input v-model="todoText" placeholder="Add your todos!" />
      <button type="submit">Add Todo</button>
    </form>
  </div>
</template>
<script lang="ts">
import { defineComponent } from 'vue';

interface Todo {
  text: string;
  done: boolean;
}

export default defineComponent({
  name: 'HelloWorld',
  data() {
    return {
      todos: [] as Todo[],
      todoText: '',
      searchQuery: ''
    };
  },
  methods: {
    addTodo(): void {
      if (this.todoText.trim() === '') return; // Prevent adding empty todos
      const newTodo: Todo = {
        text: this.todoText,
        done: false
      };
      this.todos.push(newTodo); // Use push for direct mutation which is more efficient
      localStorage.setItem('todos', JSON.stringify(this.todos));
      this.todoText = '';
    },
    removeTodo(index: number): void {
      this.todos.splice(index, 1);
      localStorage.setItem('todos', JSON.stringify(this.todos));
    }
  },
  watch: {
    todos: {
      deep: true,
      handler(newTodos: Todo[]) {
        localStorage.setItem('todos', JSON.stringify(newTodos));
      }
    }
  },
  computed: {
    filteredTodos(): Todo[] {
    // console.log('Filtering todos...', this.searchQuery);
      return this.todos.filter(todo => todo.text.includes(this.searchQuery));
    }
  },
  mounted() {
    const existingTodos = localStorage.getItem('todos');
    if (existingTodos) {
      this.todos = JSON.parse(existingTodos);
    }
  }
})
</script>
<style scoped lang="scss">
ul {
  padding: 0;
  li {
    list-style-type: none;
    margin: 10px 0;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    display: flex;
    align-items: center;
    transition: background-color 0.3s ease;

    &:hover {
      background-color: #e9e9e9;
      color: #333;
    }
    &:active {
      background-color: #d9d9d9;
    }

    .todo-label {
      display: flex;
      align-items: center;
      width: 100%;
      cursor: pointer;
    }

    input {
      margin-right: 10px;
    }

    span {
      flex-grow: 1;
    }

    button {
      padding: 5px;
      transition: background-color 0.3s ease;
      &:hover {
        background-color: #ff6464;
        color: white;
      }
    }
  }
}
form input {
  padding: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
  margin-right: 10px;
  transition: border-color 0.3s ease;
  &:hover {
    border-color: #646cff;
  }
}
//force nav to top of page and don't affect the rest of the page
nav {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background-color: #333;
  color: white;
  padding: 10px;
  display: flex;
  a {
    color: white;
    text-decoration: none;
    margin-right: 10px;
    &:hover {
      text-decoration: underline;
    }
  }
}
</style>
