<template>
    <div id="app">
      <HeaderBar />
      <UploadComponent />
    </div>
    
  </template>
  
  <script>
  import HeaderBar from './components/HeaderBar.vue'
  import UploadComponent from './components/Upload.vue'

  export default {
    name: 'App',
    components: {
      HeaderBar,
      UploadComponent
    }
  }
  </script>
  
  <style>
  
  #app {
    display: flex;
    flex-direction: column;    
  }

  </style>
  