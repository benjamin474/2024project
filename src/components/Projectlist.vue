<template>
  <div :class="{ collapsed: isCollapsed }">
    <div class="project-list">
      <ul v-if="!isCollapsed">
        <li v-for="(project, index) in projects" :key="index" @click="selectProject(project)">
          <ProjectItem :class="{ 'active': selectedProject === project }" :project="project" />
        </li>
      </ul>
      <button v-if="!isCollapsed" @click="showAddProjectModal">增加新的Project</button>
      <div class="toggle-button" @click="toggleSidebar">
        <span v-if="isCollapsed">&gt;</span>
        <span v-else>&lt;</span>
      </div>
    </div>

    <!-- 懸浮框 -->
    <div v-if="showModal" class="modal">
      <div class="modal-content">
        <span class="close" @click="closeAddProjectModal">&times;</span>
        <h2>新增工作區</h2>
        <input class="large-input" type="text" v-model="newProjectName" placeholder="輸入新的Project名稱" />
        <button @click="addProject">增加</button>
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
      isCollapsed: false,
      selectedProject: null,
      showModal: false,
      newProjectName: ""
    };
  },
  methods: {
    showAddProjectModal() {
      this.showModal = true;
    },
    closeAddProjectModal() {
      this.showModal = false;
      this.newProjectName = "";
    },
    addProject() {
      if (this.newProjectName) {
        this.projects.push({ name: this.newProjectName, files: 0 });
        this.closeAddProjectModal();
      }
    },
    selectProject(project) {
      this.selectedProject = project;
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
  border-right: 1px solid #ddd;
  padding: 10px;
  box-sizing: border-box;
  transition: width 0.3s;
  display: flex;
  flex-direction: column;
  position: relative;
  background-color: #f7f7f7;
  border-radius: 10px;
}

ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

li {
  padding: 10px;
  border-bottom: 1px solid #ddd;
  border-radius: 10px;
}

li:hover {
  background-color: #e0e0e0;
  cursor: pointer;
}

button {
  margin-top: 20px;
  padding: 10px;
  width: 100%;
  background-color: #333;
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
}

button:hover {
  background-color: #555;
}

.toggle-button {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 10px;
  height: 40px;
  background-color: #333;
  color: white;
  cursor: pointer;
  position: absolute;
  right: 0; /* 將按鈕定位在右邊邊框 */
  top: 50%;
  transform: translateY(-50%);
  transition: right 0.3s;
  border: none;
  border-radius: 5px;
}

.toggle-button span {
  font-size: 14px;
}

.collapsed .project-list {
  width: 20px; /* 調整為最小寬度以顯示切換按鈕 */
  overflow: hidden;
  border-radius: 10px;
}

.collapsed ul,
.collapsed button {
  display: none;
}

.collapsed .toggle-button {
  right: 0; /* 隱藏側邊欄時調整位置以隱藏按鈕 */
  background-color: #555;
}

.active {
  background-color: #ccc;
  border-radius: 10px;
}

/* 懸浮框樣式 */
.modal {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
  background-color: #fefefe;
  padding: 20px;
  border: 1px solid #888;
  border-radius: 10px;
  width: 300px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
  cursor: pointer;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}

.large-input {
  width: 80%;
  padding: 5px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 10px;
  margin-bottom: 20px;
}
</style>
