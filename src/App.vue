<!-- app.vue -->
<template>
  <div id="app">
    <HeaderBar class="header-bar" />
    <div class="panel">
      <Projectlist @select-project="setSelectedProject" @delete-success="refreshProjects"/>
      <component
        :is="selectedComponent"
        :project="selectedProject"
        @upload-success="refreshFiles"
      />
      <FileList
        :files="files"
        :project="selectedProject"
        @upload-success="refreshFiles"
        v-if="selectedProject && files.length > 0"
        class="file-list-container"
      />
    </div>
  </div>
</template>

<script>
import HeaderBar from "./components/HeaderBar.vue";
import UploadComponent from "./components/Upload.vue";
import Projectlist from "./components/Projectlist.vue";
import AdvancedSearch from "./components/AdvancedSearch.vue";
import FileList from "./components/Filelist.vue";

export default {
  name: "App",
  components: {
    HeaderBar,
    UploadComponent,
    Projectlist,
    AdvancedSearch,
    FileList,
  },
  data() {
    return {
      selectedProject: null,
      projects: [],
      files: [],
    };
  },
  computed: {
    selectedComponent() {
      if (this.selectedProject) {
        return this.selectedProject.files === 0
          ? "UploadComponent"
          : "AdvancedSearch";
      }
      return null;
    },
  },
  methods: {
    async setSelectedProject(project) {
      this.selectedProject = project;
      await this.fetchFiles(project.name);
    },
    async fetchProjects() {
      const data = {
        email: localStorage.getItem("email"),
        password: localStorage.getItem("password"),
      };

      try {
        const response = await fetch(
          "https://wos-data-analysis-backend.onrender.com/api/project/getAll",
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
          this.projects = result.projects;
        } else {
          console.error("獲取工作區失敗", response.statusText);
        }
      } catch (error) {
        console.error("請求失敗", error);
      }
    },
    async fetchFiles(workspaceName) {
      const data = {
        email: localStorage.getItem("email"),
        password: localStorage.getItem("password"),
        workspace: workspaceName,
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
          this.files = result.files;
          if (this.selectedProject) {
            this.selectedProject.files = result.files.length; // 更新项目的文件数量
          }
        } else {
          console.error("獲取工作區檔案失敗", response.statusText);
        }
      } catch (error) {
        console.error("請求失敗", error);
      }
    },
    async refreshFiles() {
      if (this.selectedProject) {
        await this.fetchFiles(this.selectedProject.name);
      }
    },
    async refreshProjects() {
      await this.fetchProjects();
      if (this.selectedProject) {
        await this.fetchFiles(this.selectedProject.name);
      }
    },
  },
  async mounted() {
    await this.fetchProjects();
  },
};
</script>

<style>
#app {
  position: absolute;
  top: 6%;
  height: 94%;
  width: 99.5%;
  display: flex;
  flex-direction: column;
  text-align: center;
}

.header-bar {
  flex-shrink: 0;
}

.panel {
  display: flex;
  flex-direction: row;
  width: 100%;
  flex-grow: 1;
}

.file-list-container {
  margin-left: auto;
}
</style>
