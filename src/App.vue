<template>
  <div id="app">
    <HeaderBar class="header-bar" />
    <div class="panel">
      <Projectlist  @select-project="setSelectedProject" />
      <component :is="selectedComponent" />
    </div>
  </div>
</template>
<script>
import HeaderBar from './components/HeaderBar.vue'
import UploadComponent from './components/Upload.vue'
import Projectlist from './components/Projectlist.vue'
import AdvancedSearch  from './components/AdvancedSearch.vue'

export default {
  name: 'App',
  components: {
    HeaderBar,
    UploadComponent,
    Projectlist,
    AdvancedSearch 
  },
  data() {
    return {
      selectedProject: null
    };
  },
  computed: {
    selectedComponent() {
      if (this.selectedProject) {
        return this.selectedProject.files === 0 ? 'UploadComponent' : 'AdvancedSearch';
      }
      return null;
    }
  },
  methods: {
    setSelectedProject(project) {
      this.selectedProject = project;
    }
  }
}
</script>

<style>


#app {
  position: absolute;
  top:6%;
  height:94%;
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

</style>
