<script setup>
import { ref } from "vue";

// 接收父组件传来的数据
const props = defineProps({
  todos: Array,
  trashList: Array,
});

// 定义要发出的事件
const emit = defineEmits(["delete", "restore", "hard-delete", "empty-trash"]);

const currentView = ref("tasks"); // 当前视图状态
</script>

<template>
  <div class="todo-container">
    <div class="nav-tabs">
      <button
        class="tab-btn"
        :class="{ active: currentView === 'tasks' }"
        @click="currentView = 'tasks'"
      >
        待办 ({{ todos.length }})
      </button>
      <button
        class="tab-btn"
        :class="{ active: currentView === 'trash' }"
        @click="currentView = 'trash'"
      >
        回收站 ({{ trashList.length }})
      </button>
    </div>

    <div class="scroll-area">
      <div v-if="currentView === 'tasks'">
        <div v-if="todos.length === 0" class="empty-state">
          还没有任务，添加一个开始吧！
        </div>
        <div v-for="item in todos" :key="item.id" class="todo-item">
          <input
            type="checkbox"
            v-model="item.completed"
            class="custom-checkbox"
          />
          <span class="todo-text" :class="{ 'text-done': item.completed }">
            {{ item.text }}
          </span>
          <button
            class="btn-action delete"
            @click="$emit('delete', item.id)"
            title="移入回收站"
          >
            ×
          </button>
        </div>
      </div>

      <div v-else>
        <div class="trash-header" v-if="trashList.length > 0">
          <button class="btn-clean" @click="$emit('empty-trash')">
            清空全部
          </button>
        </div>
        <div v-if="trashList.length === 0" class="empty-state">
          回收站是空的
        </div>
        <div
          v-for="item in trashList"
          :key="item.id"
          class="todo-item trash-item"
        >
          <span class="todo-text" :class="{ 'text-done': item.completed }">{{
            item.text
          }}</span>
          <div class="action-group">
            <button
              class="btn-action restore"
              @click="$emit('restore', item.id)"
              title="恢复"
            >
              恢复
            </button>
            <button
              class="btn-action hard-delete"
              @click="$emit('hard-delete', item.id)"
              title="彻底删除"
            >
              删除
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.todo-container {
  display: flex;
  flex-direction: column;
  height: 100%;
  overflow: hidden;
}
.nav-tabs {
  display: flex;
  background: #f9fafb;
  padding: 5px;
  margin-bottom: 15px;
  border-radius: 16px;
  flex-shrink: 0;
}
.tab-btn {
  flex: 1;
  padding: 8px;
  border: none;
  background: transparent;
  border-radius: 12px;
  color: #6b7280;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
}
.tab-btn.active {
  background: #fff;
  color: #6366f1;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.scroll-area {
  flex: 1;
  overflow-y: auto;
  padding-right: 5px;
}
.scroll-area::-webkit-scrollbar {
  width: 0px;
  background: transparent;
}

.empty-state {
  text-align: center;
  color: #9ca3af;
  margin-top: 40px;
  font-size: 0.9rem;
}
.todo-item {
  display: flex;
  align-items: center;
  padding: 12px 0;
  border-bottom: 1px solid #f3f4f6;
  gap: 12px;
}
.todo-item:last-child {
  border-bottom: none;
}
.todo-text {
  flex: 1;
  color: #4b5563;
  font-size: 1rem;
  transition: color 0.2s;
}
.text-done {
  text-decoration: line-through;
  color: #9ca3af;
  font-style: italic;
}

.btn-action {
  background: transparent;
  border: none;
  color: #9ca3af;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.2s;
  padding: 4px 8px;
  border-radius: 4px;
}
.btn-action:hover {
  background-color: #f3f4f6;
  color: #374151;
}
.delete:hover {
  color: #ef4444;
  background-color: #fee2e2;
}
.restore:hover {
  color: #10b981;
  background-color: #d1fae5;
}
.hard-delete:hover {
  color: #ef4444;
  background-color: #fee2e2;
}

.trash-header {
  display: flex;
  justify-content: flex-end;
  margin-bottom: 10px;
}
.btn-clean {
  background: #fee2e2;
  color: #ef4444;
  border: none;
  padding: 4px 12px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 0.8rem;
}
.custom-checkbox {
  width: 20px;
  height: 20px;
  accent-color: #6366f1;
  cursor: pointer;
}
</style>
