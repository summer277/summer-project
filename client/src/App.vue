<script setup lang="ts">
import { ref } from 'vue'

const tasks = ref<string[]>([])
const input = ref('')

const addTask = () => {
  if (input.value.trim()) {
    tasks.value.push(input.value)
    input.value = ''
  }
}
</script>

<<template>
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
      <li v-for="(task, index) in tasks" :key="index">
        {{ task }}
      </li>
    </ul>
  </div>
</template>

<style scoped>
h1 {
  color: #333;
}
</style>