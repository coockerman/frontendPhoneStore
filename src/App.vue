<template>
  <div>
    <header v-if="!showLogin" class="app-header">
      <h1>Nhân viên cửa hàng</h1>
      <nav>
        <ul>
          <li
            @click="
              resetShow();
              showSanPham = !showSanPham;
            "
          >
            Quản lý sản phẩm
          </li>
          <li
            @click="
              resetShow();
              showNCC = !showNCC;
            "
          >
            Quản lý NCC
          </li>
          <li
            @click="
              resetShow();
              showBoxAdd = !showBoxAdd;
            "
          >
            Thêm hoá đơn
          </li>
          <li>Thống kê</li>
        </ul>
      </nav>
    </header>
    <div v-if="showNCC">
      <nha-cung-cap-list />
    </div>
    <div v-if="showBoxAdd">
      <BoxAdd />
    </div>
    <div v-if="showLogin">
      <Login
        @close="setLogin"
        @login-success="loginSuccess"
        @login-failure="loginFailure"
      >
        <template v-slot:content>
          <!-- Nội dung form đăng nhập -->
        </template>
      </Login>
    </div>
    <div v-if="showSanPham">
      <SanPham />
    </div>
  </div>
</template>

<script>
import Login from "./components/Login.vue";
import SanPham from "./components/SanPham.vue";
import BoxAdd from "./components/ChucNang/BoxAdd.vue";
import NhaCungCapList from "./components/NCC/NhaCungCapList.vue";

export default {
  components: { Login, SanPham, BoxAdd, NhaCungCapList },
  data() {
    return {
      showLogin: true,
      showBoxAdd: false,
      showSanPham: false,
      showNCC: false,
      nhaCungCaps: [],
      editedNhaCungCap: null,
    };
  },
  computed: {
    formTitle() {
      return this.editedNhaCungCap ? "Sửa Nhà Cung Cấp" : "Thêm Nhà Cung Cấp";
    },
    submitButtonText() {
      return this.editedNhaCungCap ? "Cập nhật" : "Thêm mới";
    },
  },
  methods: {
    setLogin() {
      this.showLogin = !this.showLogin;
    },
    loginSuccess() {
      this.showModal = false;
    },
    loginFailure(error) {
      alert(error);
    },
    resetShow() {
      this.showBoxAdd = false;
      this.showSanPham = false;
      this.showNCC = false;
    },
  },
};
</script>

<style scoped>
/* CSS cho giao diện header */
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
