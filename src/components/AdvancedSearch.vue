<template>
  <div class="layout-container work-area-wrapper">
    <div class="section">
      <div class="search-row">
      <input type="text" placeholder="請輸入關鍵字" v-model="keyword" />
      <button @click="search">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M11 19C15.4183 19 19 15.4183 19 11C19 6.58172 15.4183 3 11 3C6.58172 3 3 6.58172 3 11C3 15.4183 6.58172 19 11 19Z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M21 21L16.65 16.65" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>
      <button @click="toggleAdvancedSearch">進階搜尋</button>
    </div>
    <div v-if="showAdvancedSearch" class="filter-row">
      <div class="filter">
        <label for="yearRange">年份區間</label>
        <input type="range" id="yearRange" v-model="yearRange" min="1900" max="2024" />
      </div>
      <div class="filter">
        <label for="citationCount">引用次數</label>
        <input type="number" id="citationCount" v-model="citationCount" placeholder="引用次数" />
      </div>
      <div class="filter">
        <label for="author">作者</label>
        <input type="text" id="author" v-model="author" placeholder="作者" />
      </div>
    </div>
    <div v-if="showAdvancedSearch" class="warning">
      <input type="checkbox" id="showDuplicateWarning" v-model="showDuplicateWarning" />
      <label for="showDuplicateWarning">顯示重複標題警告</label>
    </div>
  </div>
</div>
    
</template>

<script>
export default {
  data() {
    return {
      keyword: '',
      yearRange: 2024,
      citationCount: '',
      author: '',
      showDuplicateWarning: false,
      showAdvancedSearch: false
    };
  },
  methods: {
    search() {
      // 进阶搜索逻辑
      console.log('搜索关键字:', this.keyword);
      console.log('年份区间:', this.yearRange);
      console.log('引用次数:', this.citationCount);
      console.log('作者:', this.author);
      console.log('显示重复标题警告:', this.showDuplicateWarning);
    },
    toggleAdvancedSearch() {
      this.showAdvancedSearch = !this.showAdvancedSearch;
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
.section{
  position: absolute;
  top:3%;
}
.search-row {
  
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.search-row input[type="text"] {
  padding: 5px;
  font-size: 14px;
  margin-right: 10px;
  width: 200px;
}

.search-row button {
  padding: 5px 10px;
  font-size: 14px;
  cursor: pointer;
  display: flex;
  align-items: center;
}

.search-row button svg {
  margin-right: 5px;
}

.filter-row {
  display: flex;
  justify-content: space-between;
  width: 100%;
  margin-bottom: 20px;
  max-width: 500px;
}

.filter {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0 10px;
}

.filter label {
  margin-bottom: 5px;
  font-size: 14px;
}

.filter input[type="number"],
.filter input[type="text"] {
  padding: 5px;
  font-size: 14px;
  width: 120px;
}

.filter input[type="range"] {
  width: 200px;
}

.warning {
  display: flex;
  align-items: center;
}

.warning input[type="checkbox"] {
  margin-right: 10px;
}

.warning label {
  font-size: 14px;
}
</style>
