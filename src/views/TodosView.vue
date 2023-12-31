<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo"/>
    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem v-for="(todo, index) in todoList" 
      :todo="todo" 
      :index="index" 
      @toggle-complete="toggleTodoComplete"
      @edit-todo="toggleEditTodo"
      @update-todo="updateTodo"
      @delete-todo="deleteTodo"
      ></TodoItem>
    </ul>
    <p class="todos-message" v-else>
      <Icon icon="noto-v1:sad-but-relieved-face" color="#41b080" width="50" height="50" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
    <p class="todos-message" v-if="todoCompleted && todoList.length > 0">
      <Icon icon="noto-v1:party-popper" color="#41b080" width="50" height="50" />
      <span>You have completed all your Todos!</span>
    </p>
  </main>
</template>

<script setup>
import TodoCreator from '../components/TodoCreator.vue';
import TodoItem from '../components/TodoItem.vue';
import { ref, watch, computed } from 'vue';
import { uid } from 'uid';
import { Icon } from '@iconify/vue';

const todoList = ref([]);

watch(todoList,() => {
  setTodoListLocalStorage(); 
},
  { deep: true}
);

const todoCompleted = computed(() => {
  return todoList.value.every((todo) => todo.isCompleted);
});

const fetchTodoList = () => {
  const savedTodoList =  JSON.parse(localStorage.getItem("todoList"));
  if(savedTodoList) {
    todoList.value = savedTodoList;
  }
};

fetchTodoList();

const setTodoListLocalStorage = () => {
  localStorage.setItem("todoList", JSON.stringify(todoList.value));
};

const createTodo = (todo) => {
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: false,
    isEditing: null,
  });
  setTodoListLocalStorage();
};

const toggleTodoComplete = (index) => {
  todoList.value[index].isCompleted = !todoList.value[index].isCompleted;
};

const toggleEditTodo = (index) => {
  todoList.value[index].isEditing = !todoList.value[index].isEditing;
}

const updateTodo = (updatedValue,index) => {
  todoList.value[index].todo = updatedValue;
}

const deleteTodo = (todoId) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== todoId);
}
</script>
<style lang="scss" scoped>
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }
  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

.todos-message {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>
