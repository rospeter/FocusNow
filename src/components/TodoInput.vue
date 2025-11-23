<script setup>
import { ref } from "vue";

// 定义事件：告诉父组件，我有一个 'add' 的动作
const emit = defineEmits(["add"]);

const todoInput = ref("");

const submitTodo = () => {
  const text = todoInput.value.trim();
  if (text) {
    // 触发 add 事件，把文字传出去
    emit("add", text);
    todoInput.value = ""; // 清空输入框
  }
};
</script>

<template>
  <div class="input-group">
    <input
      type="text"
      placeholder="添加一个新专注项..."
      class="todo-input"
      v-model="todoInput"
      @keyup.enter="submitTodo"
    />
    <button class="btn-add" @click="submitTodo">＋</button>
  </div>
</template>

<style scoped>
.input-group {
  display: flex;
  align-items: center;
  background: #f9fafb;
  border: 2px solid #e5e7eb;
  border-radius: 50px;
  padding: 4px 6px 4px 20px;
  margin-bottom: 20px;
  transition: all 0.3s ease;
}
.input-group:focus-within {
  border-color: #6366f1;
  background: #fff;
  box-shadow: 0 4px 12px rgba(99, 102, 241, 0.2);
  transform: translateY(-2px);
}
.todo-input {
  flex: 1;
  border: none;
  background: transparent;
  font-size: 1rem;
  color: #374151;
  outline: none;
  padding: 8px 0;
  user-select: text;
  cursor: text;
}
.todo-input::placeholder {
  color: #9ca3af;
  font-size: 0.9rem;
}
.btn-add {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border: none;
  background: #6366f1;
  color: white;
  font-size: 1.2rem;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.2s;
  margin-left: 10px;
}
.btn-add:hover {
  background: #4f46e5;
  transform: rotate(90deg);
}
.btn-add:active {
  transform: scale(0.9);
}
</style>
