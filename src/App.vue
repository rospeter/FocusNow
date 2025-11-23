<script setup>
import { ref, watch, onMounted } from "vue";
// 引入拆分好的组件
import FocusTimer from "./components/FocusTimer.vue";
import TodoInput from "./components/TodoInput.vue";
import TodoList from "./components/TodoList.vue";

// --- 核心数据 (放在父组件管理) ---
const todos = ref([{ id: 1, text: "学习 Vue3 组件化", completed: false }]);
const trashList = ref([]);

// --- 核心操作逻辑 ---
const handleAddTodo = (text) => {
  todos.value.push({ id: Date.now(), text, completed: false });
};

const handleSoftDelete = (id) => {
  const index = todos.value.findIndex((t) => t.id === id);
  if (index > -1) {
    const item = todos.value.splice(index, 1)[0];
    trashList.value.push(item);
  }
};

const handleRestore = (id) => {
  const index = trashList.value.findIndex((t) => t.id === id);
  if (index > -1) {
    const item = trashList.value.splice(index, 1)[0];
    todos.value.push(item);
  }
};

const handleHardDelete = (id) => {
  trashList.value = trashList.value.filter((t) => t.id !== id);
};

const handleEmptyTrash = () => {
  if (confirm("确定清空回收站？")) trashList.value = [];
};

// --- 持久化存储 ---
watch(
  [todos, trashList],
  ([newTodos, newTrash]) => {
    const data = { todos: newTodos, trash: newTrash };
    localStorage.setItem("focusnow_data", JSON.stringify(data));
  },
  { deep: true }
);

onMounted(() => {
  const savedData = localStorage.getItem("focusnow_data");
  if (savedData) {
    const parsed = JSON.parse(savedData);
    if (Array.isArray(parsed)) {
      todos.value = parsed;
    } else {
      todos.value = parsed.todos || [];
      trashList.value = parsed.trash || [];
    }
  }
});
</script>

<template>
  <div class="app-container">
    <FocusTimer />

    <section class="card list-card">
      <TodoInput @add="handleAddTodo" />

      <TodoList
        :todos="todos"
        :trashList="trashList"
        @delete="handleSoftDelete"
        @restore="handleRestore"
        @hard-delete="handleHardDelete"
        @empty-trash="handleEmptyTrash"
      />
    </section>
  </div>
</template>

<style scoped>
.app-container {
  max-width: 500px;
  height: 100vh;
  margin: 0 auto;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 20px;
  user-select: none;
  cursor: default;
}

.card {
  background: #ffffff;
  border-radius: 24px;
  box-shadow: 0 10px 30px -10px rgba(0, 0, 0, 0.05);
  padding: 25px;
  transition: transform 0.2s;
}

.list-card {
  flex: 1;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  /* padding 在组件内部控制，这里不需要重复 padding */
}
</style>
