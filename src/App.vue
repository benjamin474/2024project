<template>
  <div id="app">
    <HeaderBar class="header-bar" />
    <div class="panel">
      <Projectlist class="project-list" />
      <UploadComponent class="upload-component" />
    </div>
  </div>
</template>
<script>
import HeaderBar from './components/HeaderBar.vue'
import UploadComponent from './components/Upload.vue'
import Projectlist from './components/Projectlist.vue'

export default {
  name: 'App',
  components: {
    HeaderBar,
    UploadComponent,
    Projectlist
  }
}
</script>

<style>


#app {
  position: absolute;
  top:6%;
  height:95%;
  width: 100%;
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

.project-list {
  position: absolute;
  left: 0;
  z-index: 3;
}

.upload-component {
  position: absolute;
  right: 0;
  flex-grow: 1;
}
</style>
