<template>
  <div class="project-item">
    <h3>{{ project.name }}</h3>
    <p>檔案數量: {{ project.files }}</p>
  </div>
</template>
  
  <script>
export default {
  props: {
    project: {
      type: Object,
      required: true,
    },
  },
};
</script>
  
  <style scoped>
.project-item {
  padding: 10px;
  background-color: #f9f9f9;
  border-radius: 5px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.1);
  margin-bottom: 10px;
}
</style>
  