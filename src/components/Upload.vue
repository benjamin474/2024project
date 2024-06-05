<template>
  <div class="layout-container work-area-wrapper">
    <section>
      <label class="upload-area">
        <h2 class="main-subheader">上傳你需要解析的論文</h2>
        <div class="dashes">
          <div class="brand-btn-container brand-color">
            <a class="btn-wrapper upload-btn">
              <svg xmlns="http://www.w3.org/2000/svg" width="15" height="15" fill="none">
                <path fill="#fff" fill-rule="evenodd" d="M5.6.6c-.7 0-1.3.5-1.3 1.2v2.5H1.8a1.3 1.3 0 000 2.5h2.5v2.5a1.3 1.3 0 102.5 0V6.8h2.5a1.2 1.2 0 100-2.5H6.8V1.8c0-.7-.5-1.2-1.2-1.2z" clip-rule="evenodd"></path>
              </svg>
              <span class="btn-label">選擇檔案</span>
            </a>
          </div>
          <input class="file-upload-input" type="file" multiple @change="handleFiles" ref="fileInput">
        </div>
      </label>
      <div class="file-list">
        <div v-for="file in files" :key="file.name" class="file-item">
          <span>{{ file.name }}</span>
          <span>{{ (file.size / 1024).toFixed(2) }} KB</span>
        </div>
      </div>
      <div class="action-buttons">
        <button @click="convertFiles">開始轉換</button>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      zoomLevel: 2,
      files: []
    };
  },
  methods: {
    handleFiles(event) {
      this.files = Array.from(event.target.files);
      console.log(this.files);
    },
    convertFiles() {
      if (this.files.length > 0) {
        // Conversion logic here
        alert("開始轉換文件");
      } else {
        alert("請先上傳文件");
      }
    }
  }
};
</script>

<style scoped>
.layout-container {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.work-area-wrapper {
  width: 100%;
}

.zoom-slider {
  display: flex;
  align-items: center;
}

.upload-area {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.dashes {
  width: 300px;
  border: 2px solid #ccc; 
  background: rgb(156, 153, 153);
  border-radius: 15px; 
  padding: 30px;
  text-align: center;
  display: flex; 
  flex-direction: column; 
  align-items: center; 
  justify-content: center; 
}

.btn-label {
  color: aliceblue;
}

.main-subheader {
  margin-bottom: 20px;
}

.btn-wrapper {
  display: flex;
  align-items: center;
  cursor: pointer;
}

.file-upload-input {
  display: none;
}

.file-list {
  margin-top: 20px;
}

.file-item {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}

.action-buttons {
  margin-top: 20px;
}

.action-buttons button {
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  cursor: pointer;
}

.action-buttons button:hover {
  background-color: #0056b3;
}
</style>
