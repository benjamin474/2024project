<template>
  <div class="project-list">
    <ul>
      <li v-for="(project, index) in projects" :key="index">
        <ProjectItem :project="project" />
      </li>
    </ul>
    <button @click="addProject">增加新的Project</button>
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
        { name: "Project 1", files: 5 },
        { name: "Project 2", files: 3 },
        { name: "Project 3", files: 8 }
      ]
    };
  },
  methods: {
    addProject() {
      const newProjectName = prompt("輸入新的Project name");
      if (newProjectName) {
        this.projects.push({ name: newProjectName, files: 0 });
      }
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
</style>
