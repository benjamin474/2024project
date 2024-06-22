<template>
  <div :class="{ collapsed: isCollapsed }">
    <div class="project-list">
      <ul v-if="!isCollapsed">
        <li
          v-for="(project, index) in projects"
          :key="index"
          @click="selectProject(project)"
        >
          <ProjectItem
            :class="{ active: selectedProject === project }"
            :project="project"
          />
        </li>
      </ul>
      <button v-if="!isCollapsed" @click="showAddProjectModal">
        增加新的Project
      </button>
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
        <input
          class="large-input"
          type="text"
          v-model="newProjectName"
          placeholder="輸入新的Project名稱"
        />
        <button @click="addProject">增加</button>
      </div>
    </div>
  </div>
</template>

<script>
import ProjectItem from "./ProjectItem.vue";

export default {
  components: {
    ProjectItem,
  },
  data() {
    return {
      projects: [],
      isCollapsed: false,
      selectedProject: null,
      showModal: false,
      newProjectName: "",
    };
  },
  async mounted() {
    await this.fetchWorkspaces();
  },
  methods: {
    async fetchWorkspaces() {
      const data = {
        email: localStorage.getItem("email"),
        password: localStorage.getItem("password"),
      };

      try {
        const response = await fetch(
          "https://wos-data-analysis-backend.onrender.com/api/file/getWorkspace",
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify(data),
          }
        );

        if (response.ok) {
          const result = await response.json();
          this.projects = result.workspace.map((workspace) => ({
            name: workspace.name,
            files: workspace.count,
          }));
          console.log(this.projects);
        } else {
          console.error("獲取工作區失敗", response.statusText);
        }
      } catch (error) {
        console.error("請求失敗", error);
      }
    },
    showAddProjectModal() {
      this.showModal = true;
    },
    closeAddProjectModal() {
      this.showModal = false;
      this.newProjectName = "";
    },
    async addProject() {
      if (this.newProjectName) {
        const data = {
          email: localStorage.getItem("email"),
          password: localStorage.getItem("password"),
          name: this.newProjectName,
        };

        try {
          const response = await fetch(
            "https://wos-data-analysis-backend.onrender.com/api/file/newWorkspace",
            {
              method: "POST",
              headers: {
                "Content-Type": "application/json",
              },
              body: JSON.stringify(data),
            }
          );

          if (response.ok) {
            const result = await response.json();
            this.projects.push({ name: result.name, files: 0 });
            this.closeAddProjectModal();
            alert("工作區新增成功");
          } else {
            console.error("新增工作區失敗", response.statusText);
            alert("新增工作區失敗");
          }
        } catch (error) {
          console.error("請求失敗", error);
          alert("請求失敗");
        }
      }
    },
    async selectProject(project) {
      this.$emit("select-project", project);
      this.selectedProject = project;

      const data = {
        email: localStorage.getItem("email"),
        password: localStorage.getItem("password"),
        workspace: project.name,
      };

      try {
        const response = await fetch(
          "https://wos-data-analysis-backend.onrender.com/api/file/getFolder",
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify(data),
          }
        );

        if (response.ok) {
          const result = await response.json();
          project.files = result.files.length; // 更新项目的文件数量
          this.$emit("update-files", result.files);
          console.log("工作區底下的檔案:", result.files);
        } else {
          console.error("獲取工作區檔案失敗", response.statusText);
        }
      } catch (error) {
        console.error("請求失敗", error);
      }
    },
    toggleSidebar() {
      this.isCollapsed = !this.isCollapsed;
    },
  },
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
  right: 0;
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
  width: 20px;
  overflow: hidden;
  border-radius: 10px;
}

.collapsed ul,
.collapsed button {
  display: none;
}

.collapsed .toggle-button {
  right: 0;
  background-color: #555;
}

.active {
  background-color: #ccc;
  border-radius: 10px;
}

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
