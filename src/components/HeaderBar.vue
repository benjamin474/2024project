<!-- HeaderBar.vue -->

<template>
  <div class="header-bar">
    <div class="title">
      <img src="/src/assets/icon.jpg" width="40" height="40" />
      Data Analysis
    </div>
    <div class="auth-buttons">
      <button v-if="!isLoggedIn" @click="showLoginModal">登入</button>
      <button v-if="!isLoggedIn" class="sign" @click="showRegisterModal">
        註冊
      </button>
      <button v-if="isLoggedIn" @click="logout">登出</button>
    </div>

    <!-- 登入 Modal -->
    <div v-if="isLoginModalVisible" class="modal-overlay" @click="hideModal">
      <div class="modal-content" @click.stop>
        <h2>登入</h2>
        <input type="email" v-model="loginEmail" placeholder="電子郵件" />
        <input type="password" v-model="loginPassword" placeholder="密碼" />
        <button @click="login">登入</button>
      </div>
    </div>

    <!-- 註冊 Modal -->
    <div v-if="isRegisterModalVisible" class="modal-overlay" @click="hideModal">
      <div class="modal-content" @click.stop>
        <h2>註冊</h2>
        <input type="email" v-model="registerEmail" placeholder="電子郵件" />
        <input type="password" v-model="registerPassword" placeholder="密碼" />
        <button @click="register">註冊</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HeaderBar",
  data() {
    return {
      isLoginModalVisible: false,
      isRegisterModalVisible: false,
      isLoggedIn: false,
      loginEmail: "",
      loginPassword: "",
      registerEmail: "",
      registerPassword: "",
    };
  },
  mounted() {
    // 檢查 localStorage 中是否有登入資訊
    const storedEmail = localStorage.getItem("email");
    const storedPassword = localStorage.getItem("password");
    if (storedEmail && storedPassword) {
      this.isLoggedIn = true;
    }
  },
  methods: {
    showLoginModal() {
      this.isLoginModalVisible = true;
    },
    showRegisterModal() {
      this.isRegisterModalVisible = true;
    },
    hideModal() {
      this.isLoginModalVisible = false;
      this.isRegisterModalVisible = false;
    },
    validateEmail(email) {
      const re = /^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$/;
      return re.test(email);
    },
    validatePassword(password) {
      const re = /^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{6,}$/;
      return re.test(password);
    },
    async register() {
      if (!this.validateEmail(this.registerEmail)) {
        alert("請輸入有效的電子郵件地址");
        return;
      }
      if (!this.validatePassword(this.registerPassword)) {
        alert("密碼至少包含6個字符，包括英文字母和數字");
        return;
      }

      const userData = {
        email: this.registerEmail,
        password: this.registerPassword,
      };

      try {
        const response = await fetch(
          "https://wos-data-analysis-backend.onrender.com/api/auth/register",
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify(userData),
          }
        );

        const result = await response.json();

        if (response.ok) {
          alert(`註冊成功: ${result.message}`);
          this.hideModal();
        } else {
          throw new Error(result.message);
        }
      } catch (error) {
        alert(`註冊失敗: ${error.message}`);
      }
    },
    async login() {
      const userData = {
        email: this.loginEmail,
        password: this.loginPassword,
      };

      try {
        const response = await fetch(
          "https://wos-data-analysis-backend.onrender.com/api/auth/login",
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify(userData),
          }
        );

        const result = await response.json();

        if (response.ok) {
          alert(`登入成功: ${result.message}`);
          this.isLoggedIn = true;

          // 存儲到 localStorage
          localStorage.setItem("email", result.email);
          localStorage.setItem("password", result.password);

          this.hideModal();
          window.location.reload(); // 重新整理頁面
        } else {
          throw new Error(result.message);
        }
      } catch (error) {
        alert(`登入失敗: ${error.message}`);
      }
    },
    logout() {
      this.isLoggedIn = false;
      localStorage.removeItem("email");
      localStorage.removeItem("password");
      alert("已登出");
      window.location.reload(); // 重新整理頁面
    },
  },
};
</script>

<style scoped>
.header-bar {
  width: 100%;
  height: 5%;
  border-bottom: 1px solid silver;
  border-left: 1px solid silver;
  border-right: 1px solid silver;
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
  margin-right: 5px;
  margin-left: 10px;
  padding: 10px 20px;
  background-color: #fff;
  color: #333;
  border: none;
  cursor: pointer;
}
.auth-buttons .sign {
  margin-right: 10px;
  margin-left: 10px;
  padding: 10px 20px;
  color: #e74c3c;
  border: 2px solid;
  border-radius: 100px;
  background-color: #fff;
  transition: all 150ms linear !important;
}
.auth-buttons button:hover {
  color: #ccc;
}
.auth-buttons .sign:hover {
  background-color: #000000;
  border: 2px solid;
  color: #fff;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background: #fff;
  padding: 20px;
  border-radius: 5px;
  width: 300px;
  text-align: center;
}

.modal-content h2 {
  margin-bottom: 20px;
}

.modal-content input {
  display: block;
  width: calc(100% - 20px);
  margin: 10px auto;
  padding: 10px;
}

.modal-content button {
  margin-top: 10px;
  padding: 10px 20px;
  background-color: #e74c3c;
  color: #fff;
  border: none;
  cursor: pointer;
}

.modal-content button:hover {
  background-color: #c0392b;
}
</style>
