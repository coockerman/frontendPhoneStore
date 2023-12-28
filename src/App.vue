<template>
  <div>
    <div v-if="showLogin">
      <Login
        @SetQuanLy="setLoginQuanLy"
        @SetNhanVien="setLoginNhanVien"
        @login-success="loginSuccess"
        @login-failure="loginFailure"
      >
      </Login>
    </div>
    <div v-if="showNhanVien" >
      <NhanVien @logout="setLoginNhanVien"/>
    </div>
    <div v-if="showQuanLy">
      <QuanLy @logout="setLoginQuanLy"/>
    </div>
  </div>
</template>

<script>
import Login from "./components/Login.vue";
import NhanVien from "./components/NV/NhanVien.vue";
import QuanLy from "./components/QL/QuanLy.vue";

export default {
  components: { Login, NhanVien, QuanLy },
  data() {
    return {
      showLogin: true,
      showNhanVien: false,
      showQuanLy: false,
    };
  },
  methods: {
    setLoginQuanLy() {
      this.showLogin = !this.showLogin;
      this.showQuanLy = !this.showQuanLy;
    },
    setLoginNhanVien() {
      this.showLogin = !this.showLogin;
      this.showNhanVien = !this.showNhanVien;
    },
    loginSuccess() {},
    loginFailure(error) {

    },
    resetShow() {

    },
  },
};
</script>

<style scoped>
.app-header {
  background-color: #26899c;
  color: #ffffff;
  padding: 15px;
  text-align: center;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.app-header h1 {
  margin: 0;
  font-size: 28px;
}

nav {
  margin-top: 10px;
}

ul {
  list-style: none;
  padding: 0;
  display: flex;
  justify-content: center;
}

li {
  margin-right: 20px;
  cursor: pointer;
  font-size: 18px;
  transition: color 0.3s;
}

li:hover {
  color: #e74c3c;
  text-decoration: underline;
}
</style>
