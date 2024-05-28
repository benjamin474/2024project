<template>
  <div class="header-bar">
    <div class="title">Data Analysis</div>
    <div class="auth-buttons">
      <button @click="login">登入</button>
      <button @click="register">註冊</button>
    </div>
    
  </div>
</template>

<script>
export default {
  name: 'HeaderBar',
  methods: {
    login() {
      // 登入邏輯
      alert('登入');
    },
    register() {
      // 註冊邏輯
      alert('註冊');
    }
  }
}
</script>

<style scoped>
.header-bar {
  width: 100%;
  border:1px solid silver;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 35px 0px;
  background-color: #fff;
  color: #505050;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1000;
}

.title {
  margin-left: 10px;
  font-size: 2em;
}

.auth-buttons button {
  margin-right: 10px;
  margin-left: 10px;
  padding: 10px 20px;
  background-color: #fff;
  color: #333;
  border: none;
  cursor: pointer;
}

.auth-buttons button:hover {
  background-color: #ddd;
}
</style>
s