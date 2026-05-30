<script setup lang="ts">
import { ref, onMounted } from 'vue'

const tasks = ref<any[]>([])
const input = ref('')
const editIndex = ref(-1)
const editValue = ref('')
const API_URL = 'http://localhost:3001/api/tasks'

// 页面加载时获取任务列表
onMounted(async () => {
  const res = await fetch(API_URL)
  tasks.value = await res.json()
})

const addTask = async () => {
  if (input.value.trim()) {
    const res = await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ content: input.value })
    })
    const newTask = await res.json()
    tasks.value.unshift(newTask)
    input.value = ''
  }
}

const deleteTask = async (id: string, index: number) => {
  await fetch(`${API_URL}/${id}`, { method: 'DELETE' })
  tasks.value.splice(index, 1)
}

const startEdit = (index: number) => {
  editIndex.value = index
  editValue.value = tasks.value[index].content
}

const saveEdit = async () => {
  if (editValue.value.trim() && editIndex.value >= 0) {
    const task = tasks.value[editIndex.value]
    const res = await fetch(`${API_URL}/${task._id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ content: editValue.value })
    })
    const updated = await res.json()
    tasks.value[editIndex.value].content = updated.content
  }
  editIndex.value = -1
  editValue.value = ''
}
</script>

<template>
  <div style="padding: 20px; max-width: 500px; margin: 0 auto;">
    <h1>任务管理系统</h1>

    <div style="display: flex; gap: 10px; margin-bottom: 20px;">
      <input
          v-model="input"
          placeholder="输入任务..."
          style="flex: 1; padding: 8px;"
      />
      <button @click="addTask" style="padding: 8px 16px;">
        添加
      </button>
    </div>

    <ul>
      <li v-for="(task, index) in tasks" :key="task._id" style="margin-bottom: 8px;">
        <span v-if="editIndex !== index">{{ task.content }}</span>
        <input v-else v-model="editValue" @keyup.enter="saveEdit" style="padding: 4px;" />

        <button v-if="editIndex !== index" @click="startEdit(index)" style="margin-left: 10px;">
          编辑
        </button>
        <button v-else @click="saveEdit" style="margin-left: 10px;">
          保存
        </button>

        <button @click="deleteTask(task._id, index)" style="margin-left: 5px; color: red;">
          删除
        </button>
      </li>
    </ul>
  </div>
</template>

<style scoped>
h1 {
  color: #333;
}
</style>