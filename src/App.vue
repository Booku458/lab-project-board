<script setup>
import { ref, computed, watch } from 'vue'

// 默认项目数据（当 localStorage 没有数据时使用）
const defaultProjects = [
  {
    id: 1,
    name: '智能天气助手',
    description: '一个可以查看天气预报和空气质量的应用',
    members: ['张三', '李四'],
    techStack: ['Vue 3', 'Node.js', 'MongoDB'],
    status: '进行中',
    github: 'https://github.com/example/weather-app'
  },
  {
    id: 2,
    name: '在线笔记系统',
    description: '支持 Markdown 格式的在线笔记应用',
    members: ['王五', '赵六', '钱七'],
    techStack: ['React', 'Express', 'MySQL'],
    status: '已完成',
    github: 'https://github.com/example/note-app'
  },
  {
    id: 3,
    name: '任务管理工具',
    description: '团队协作的任务管理和进度跟踪工具',
    members: ['孙八', '周九'],
    techStack: ['Vue 3', 'Firebase'],
    status: '暂停',
    github: 'https://github.com/example/task-manager'
  }
]

// 从 localStorage 读取数据，如果没有则使用默认数据
function loadProjects() {
  const saved = localStorage.getItem('lab-projects')
  if (saved) {
    return JSON.parse(saved)
  }
  return defaultProjects
}

// 项目列表数据（从 localStorage 初始化）
const projects = ref(loadProjects())

// 监听 projects 变化，自动保存到 localStorage
watch(projects, (newProjects) => {
  localStorage.setItem('lab-projects', JSON.stringify(newProjects))
}, { deep: true })

// 搜索关键词
const searchKeyword = ref('')

// 控制表单显示/隐藏
const showForm = ref(false)

// 表单数据
const formData = ref({
  name: '',
  description: '',
  members: '',
  techStack: '',
  status: '进行中',
  github: ''
})

// 根据搜索关键词过滤项目列表
const filteredProjects = computed(() => {
  // 如果没有搜索关键词，显示所有项目
  if (!searchKeyword.value.trim()) {
    return projects.value
  }

  // 将关键词转为小写，实现不区分大小写的搜索
  const keyword = searchKeyword.value.toLowerCase()

  // 过滤项目：项目名称包含关键词
  return projects.value.filter(project =>
    project.name.toLowerCase().includes(keyword)
  )
})

// 根据状态返回对应的 CSS 类名
function getStatusClass(status) {
  const classMap = {
    '进行中': 'status-progress',
    '已完成': 'status-done',
    '暂停': 'status-paused'
  }
  return classMap[status] || ''
}

// 显示新增项目表单
function showAddForm() {
  showForm.value = true
}

// 关闭表单
function closeForm() {
  showForm.value = false
  resetForm()
}

// 重置表单
function resetForm() {
  formData.value = {
    name: '',
    description: '',
    members: '',
    techStack: '',
    status: '进行中',
    github: ''
  }
}

// 提交表单
function submitForm() {
  // 简单验证
  if (!formData.value.name || !formData.value.description) {
    alert('请填写项目名称和简介')
    return
  }

  // 创建新项目
  const newProject = {
    id: Date.now(), // 使用时间戳作为唯一ID
    name: formData.value.name,
    description: formData.value.description,
    members: formData.value.members.split(',').map(m => m.trim()).filter(m => m),
    techStack: formData.value.techStack.split(',').map(t => t.trim()).filter(t => t),
    status: formData.value.status,
    github: formData.value.github
  }

  // 添加到列表
  projects.value.push(newProject)

  // 关闭表单并重置
  closeForm()
}

// 删除项目
function deleteProject(id) {
  // 确认删除
  if (confirm('确定要删除这个项目吗？')) {
    // 从数组中移除该项目
    projects.value = projects.value.filter(project => project.id !== id)
  }
}
</script>

<template>
  <div class="app">
    <header class="header">
      <h1>实验室项目展示板</h1>
      <button class="add-btn" @click="showAddForm">+ 新增项目</button>
    </header>

    <!-- 搜索框 -->
    <div class="search-bar">
      <input
        v-model="searchKeyword"
        type="text"
        placeholder="搜索项目名称..."
        class="search-input"
      />
    </div>

    <main class="main">
      <!-- 新增项目表单 -->
      <div v-if="showForm" class="form-overlay" @click.self="closeForm">
        <div class="form-container">
          <h2>新增项目</h2>
          <form @submit.prevent="submitForm">
            <div class="form-group">
              <label>项目名称 *</label>
              <input v-model="formData.name" type="text" placeholder="请输入项目名称" required />
            </div>

            <div class="form-group">
              <label>项目简介 *</label>
              <textarea v-model="formData.description" placeholder="请输入项目简介" rows="3" required></textarea>
            </div>

            <div class="form-group">
              <label>项目成员</label>
              <input v-model="formData.members" type="text" placeholder="用逗号分隔，如：张三, 李四" />
            </div>

            <div class="form-group">
              <label>技术栈</label>
              <input v-model="formData.techStack" type="text" placeholder="用逗号分隔，如：Vue 3, Node.js" />
            </div>

            <div class="form-group">
              <label>项目状态</label>
              <select v-model="formData.status">
                <option value="进行中">进行中</option>
                <option value="已完成">已完成</option>
                <option value="暂停">暂停</option>
              </select>
            </div>

            <div class="form-group">
              <label>GitHub 地址</label>
              <input v-model="formData.github" type="text" placeholder="https://github.com/..." />
            </div>

            <div class="form-actions">
              <button type="button" class="cancel-btn" @click="closeForm">取消</button>
              <button type="submit" class="submit-btn">提交</button>
            </div>
          </form>
        </div>
      </div>

      <!-- 项目列表 -->
      <div class="project-list">
        <div v-for="project in filteredProjects" :key="project.id" class="project-card">
          <div class="card-header">
            <h2 class="project-name">{{ project.name }}</h2>
            <button class="delete-btn" @click="deleteProject(project.id)">删除</button>
          </div>
          <p class="project-desc">{{ project.description }}</p>

          <div class="project-info">
            <div class="info-item">
              <span class="label">项目成员：</span>
              <span>{{ project.members.join('、') }}</span>
            </div>

            <div class="info-item">
              <span class="label">技术栈：</span>
              <span class="tech-tags">
                <span v-for="tech in project.techStack" :key="tech" class="tech-tag">
                  {{ tech }}
                </span>
              </span>
            </div>

            <div class="info-item">
              <span class="label">项目状态：</span>
              <span class="status" :class="getStatusClass(project.status)">
                {{ project.status }}
              </span>
            </div>

            <div class="info-item">
              <span class="label">GitHub：</span>
              <a :href="project.github" target="_blank" class="github-link">
                查看代码
              </a>
            </div>
          </div>
        </div>

        <!-- 没有搜索结果时显示 -->
        <div v-if="filteredProjects.length === 0 && searchKeyword.trim()" class="no-results">
          没有找到匹配的项目
        </div>
      </div>
    </main>
  </div>
</template>

<style scoped>
.app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 40px;
}

.header h1 {
  color: #2c3e50;
  font-size: 2.5em;
  margin: 0;
}

.add-btn {
  background: #3498db;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  font-size: 1em;
  cursor: pointer;
  transition: background 0.2s;
}

.add-btn:hover {
  background: #2980b9;
}

/* 搜索框样式 */
.search-bar {
  margin-bottom: 30px;
}

.search-input {
  width: 100%;
  padding: 12px 16px;
  border: 2px solid #ddd;
  border-radius: 8px;
  font-size: 1em;
  transition: border-color 0.2s;
}

.search-input:focus {
  outline: none;
  border-color: #3498db;
}

.main {
  min-height: 400px;
}

/* 表单样式 */
.form-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.form-container {
  background: white;
  border-radius: 12px;
  padding: 32px;
  width: 90%;
  max-width: 600px;
  max-height: 90vh;
  overflow-y: auto;
}

.form-container h2 {
  margin: 0 0 24px 0;
  color: #2c3e50;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  color: #2c3e50;
  font-weight: 500;
}

.form-group input,
.form-group textarea,
.form-group select {
  width: 100%;
  padding: 10px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 1em;
  font-family: inherit;
  transition: border-color 0.2s;
}

.form-group input:focus,
.form-group textarea:focus,
.form-group select:focus {
  outline: none;
  border-color: #3498db;
}

.form-actions {
  display: flex;
  gap: 12px;
  justify-content: flex-end;
  margin-top: 24px;
}

.cancel-btn {
  background: #95a5a6;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 6px;
  cursor: pointer;
  transition: background 0.2s;
}

.cancel-btn:hover {
  background: #7f8c8d;
}

.submit-btn {
  background: #27ae60;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 6px;
  cursor: pointer;
  transition: background 0.2s;
}

.submit-btn:hover {
  background: #229954;
}

.project-list {
  display: grid;
  gap: 20px;
}

.project-card {
  background: white;
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s, box-shadow 0.2s;
}

.project-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.project-name {
  color: #2c3e50;
  font-size: 1.8em;
  margin: 0;
}

.delete-btn {
  background: #e74c3c;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 6px;
  font-size: 0.9em;
  cursor: pointer;
  transition: background 0.2s;
}

.delete-btn:hover {
  background: #c0392b;
}

.project-desc {
  color: #666;
  font-size: 1.1em;
  margin: 0 0 20px 0;
  line-height: 1.6;
}

.project-info {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.info-item {
  display: flex;
  align-items: center;
  gap: 8px;
}

.label {
  color: #7f8c8d;
  font-weight: 500;
  min-width: 80px;
}

.tech-tags {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}

.tech-tag {
  background: #3498db;
  color: white;
  padding: 4px 12px;
  border-radius: 16px;
  font-size: 0.9em;
}

.status {
  padding: 4px 12px;
  border-radius: 16px;
  font-size: 0.9em;
  font-weight: 500;
}

.status-progress {
  background: #f39c12;
  color: white;
}

.status-done {
  background: #27ae60;
  color: white;
}

.status-paused {
  background: #95a5a6;
  color: white;
}

.github-link {
  color: #3498db;
  text-decoration: none;
  font-weight: 500;
}

.github-link:hover {
  text-decoration: underline;
}

/* 没有搜索结果提示 */
.no-results {
  text-align: center;
  padding: 40px;
  color: #7f8c8d;
  font-size: 1.1em;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .header h1 {
    font-size: 2em;
  }

  .project-card {
    padding: 16px;
  }

  .project-name {
    font-size: 1.5em;
  }
}
</style>
