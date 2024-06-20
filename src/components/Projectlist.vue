<template>
  <div :class="{ collapsed: isCollapsed }">
    <div class="project-list">
      <ul v-if="!isCollapsed">
        <li v-for="(project, index) in projects" :key="index" @click="selectProject(project)">
          <ProjectItem :project="project" />
        </li>
      </ul>
      <button v-if="!isCollapsed" @click="addProject">增加新的Project</button>
      <div class="toggle-button" @click="toggleSidebar">
        <span v-if="isCollapsed">&gt;</span>
        <span v-else>&lt;</span>
      </div>
    </div>
  </div>
</template>

<script>
import ProjectItem from './ProjectItem.vue';

export default {
  components: {
    ProjectItem
  },
  data() {
    return {
      projects: [
        { name: "Project 1", files: 0 },
        { name: "Project 2", files: 3 },
        { name: "Project 3", files: 8 }
      ],
      isCollapsed: false
    };
  },
  methods: {
    addProject() {
      const newProjectName = prompt("输入新的Project名称");
      if (newProjectName) {
        this.projects.push({ name: newProjectName, files: 0 });
      }
    },
    selectProject(project) {
      this.$emit('select-project', project);
    },
    toggleSidebar() {
      this.isCollapsed = !this.isCollapsed;
    }
  }
};
</script>

<style scoped>
.project-list {
  width: 250px;
  height: 100%;
  border-right: 1px solid #ccc;
  padding: 10px;
  box-sizing: border-box;
  transition: width 0.3s;
  display: flex;
  flex-direction: column;
  position: relative;
}

ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

li {
  padding: 10px 0;
  border-bottom: 1px solid #ccc;
}

li:hover {
  background-color: #f0f0f0;
  cursor: pointer;
}

button {
  margin-top: 20px;
  padding: 10px;
  width: 100%;
  background-color: #007bff;
  color: white;
  border: none;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

.toggle-button {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 5px;
  height: 40px;
  background-color: #007bff;
  color: white;
  cursor: pointer;
  position: absolute;
  right: 0; /* 將按鈕定位在右邊邊框 */
  top: 50%;
  transform: translateY(-50%);
  transition: right 0.3s;
}

.toggle-button span {
  font-size: 14px;
}

.collapsed .project-list {
  width: 20px; /* 調整為最小寬度以顯示切換按鈕 */
  overflow: hidden;
}

.collapsed ul,
.collapsed button {
  display: none;
}

.collapsed .toggle-button {
  right: 0; /* 隱藏側邊欄時調整位置以隱藏按鈕 */
  background-color: #0056b3;
}
</style>
