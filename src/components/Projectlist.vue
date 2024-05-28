<template>
    <div class="project-list">
      <ul>
        <li v-for="(project, index) in projects" :key="index">{{ project.name }}</li>
      </ul>
      <button @click="addProject">增加项目</button>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        projects: [
          { name: "项目 1" },
          { name: "项目 2" },
          { name: "项目 3" }
        ]
      };
    },
    methods: {
      addProject() {
        const newProjectName = prompt("输入新项目的名称:");
        if (newProjectName) {
          this.projects.push({ name: newProjectName });
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
  