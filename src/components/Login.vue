<template>
  <div class="modal-container" v-show="isOpen">
    <div class="modal-content" @click.stop>
      <button class="close-button" @click="closeModal">&times;</button>
      <slot name="content">
        <h2>Đăng nhập</h2>
        <form @submit.prevent="login">
          <div class="input-container">
            <label for="username">Tên đăng nhập:</label>
            <input v-model="username" id="username" type="text" />
          </div>
          <div class="input-container">
            <label for="password">Mật khẩu:</label>
            <input v-model="password" id="password" type="password" />
          </div>
          <button type="submit">Đăng nhập</button>
        </form>
        <div v-if="loginError" class="error-message">
          {{ loginError }}
        </div>
      </slot>
      <div v-if="error" class="error-message">
        {{ error }}
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      isOpen: true,
      username: "",
      password: "",
      loginError: "",
      apiUrl: "http://localhost:8081/login", // Thay đổi địa chỉ API của bạn nếu cần
      error: "", // Thêm biến để lưu thông báo lỗi
    };
  },
  methods: {
    openModal() {
      this.isOpen = true;
    },
    closeModal() {
      this.isOpen = false;
      this.$emit("close");
    },
    async login() {
      // Chuẩn bị dữ liệu đăng nhập
      const loginData = {
        userName: String(this.username),
        password: String(this.password)
      };
      // Gọi API đăng nhập
      axios
        .post("http://localhost:8081/api/v1/authenticate", loginData)
        .then((response) => {
          // Xử lý phản hồi từ API
          const result = response.data;
          if (String(result) == "true") {
            console.log("Đăng nhập thành công");
            this.$emit('close');
          } else {
            // Đăng nhập thất bại, lưu thông báo lỗi để hiển thị
            this.error =
              "Kiểm tra lại tài khoản, mật khẩu";
          }
        })
        .catch((error) => {
          console.error("Lỗi kết nối đến API", error);
          // Lưu thông báo lỗi để hiển thị
          this.error = "Lỗi kết nối đến máy chủ";
        });
    },
    

    // ... (các methods khác của bạn) ...
  },
};
</script>

  <style scoped>
.modal-container {
  display: flex;
  align-items: center;
  justify-content: center;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 999;
}

.modal-content {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  max-width: 400px;
  width: 100%;
}

.close-button {
  position: absolute;
  top: 10px;
  right: 10px;
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
  color: #555;
}
</style>
  