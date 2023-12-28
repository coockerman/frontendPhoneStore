<template>
  <div class="user-form">
    <h2>Thêm/sửa User</h2>
    <div class="input-container">
      <label for="userName">Tên đăng nhập:</label>
      <input v-model="userName" id="userName" type="text" />
    </div>
    <div class="input-container">
      <label for="password">Mật khẩu:</label>
      <input v-model="password" id="password" type="password" />
    </div>
    <div class="input-container">
      <label for="ten">Họ tên:</label>
      <input v-model="ten" id="ten" type="text" />
    </div>
    <div class="input-container">
      <label for="diaChi">Địa chỉ:</label>
      <input v-model="diaChi" id="diaChi" type="text" />
    </div>
    <div class="input-container">
      <label for="sdt">Số điện thoại:</label>
      <input v-model="sdt" id="sdt" type="text" />
    </div>
    <div class="input-container">
      <label for="chucVu">Chức vụ:</label>
      <select v-model="chucVu" id="chucVu">
        <option value="NhanVien">Nhân Viên</option>
        <option value="QuanLy">Quản Lý</option>
      </select>
    </div>
    <div class="input-container">
      <label for="gioiTinh">Giới tính:</label>
      <input v-model="gioiTinh" id="gioiTinh" type="text" />
    </div>

    <button @click="saveUser">Lưu</button>

    <!-- Hiển thị thông báo lỗi -->
    <div v-if="error" class="error-message">
      {{ error }}
    </div>
  </div>
</template>
  
  <script>
import axios from "axios";

export default {
  data() {
    return {
      userName: "",
      password: "",
      ten: "",
      diaChi: "",
      sdt: "",
      chucVu: "",
      gioiTinh: "",
      error: null,
      editingUserId: null,
    };
  },
  methods: {
    async saveUser() {
      // Kiểm tra các trường dữ liệu
      if (
        !this.userName ||
        !this.password ||
        !this.ten ||
        !this.diaChi ||
        !this.sdt ||
        !this.chucVu ||
        !this.gioiTinh
      ) {
        this.error = "Vui lòng nhập đầy đủ thông tin.";
        return;
      }

      const userData = {
        userName: this.userName,
        password: String(this.password),
        ten: this.ten,
        diaChi: this.diaChi,
        sdt: this.sdt,
        chucVu: this.chucVu,
        gioiTinh: this.gioiTinh,
      };

      try {
        if (this.editingUserId) {
          // Nếu đang sửa người dùng, gửi yêu cầu HTTP để cập nhật người dùng
          await axios.put(
            `http://localhost:8081/api/v1/user/update/${this.editingUserId}`,
            userData
          );
        } else {
          // Nếu không có editingUserId, đây là thêm mới người dùng
          await axios.post("http://localhost:8081/api/v1/user/save", userData);
        }

        // Đặt lại form và tải lại danh sách người dùng
        this.resetForm();
        this.$emit("user-updated");
      } catch (error) {
        console.error("Error saving user:", error);
        this.error = "Đã xảy ra lỗi khi lưu người dùng.";
      }
    },
    editUser(user) {
      // Đưa dữ liệu người dùng cần sửa vào form
      this.userName = user.userName;
      this.password = user.password;
      this.ten = user.ten;
      this.diaChi = user.diaChi;
      this.sdt = user.sdt;
      this.chucVu = user.chucVu;
      this.gioiTinh = user.gioiTinh;
      // Gán giá trị cho editingUserId để xác định là đang sửa người dùng hiện tại
      this.editingUserId = user.id;
    },
    resetForm() {
      this.userName = "";
      this.password = "";
      this.ten = "";
      this.diaChi = "";
      this.sdt = "";
      this.chucVu = "";
      this.gioiTinh = "";
      this.editingUserId = null;
      this.error = null;
    },
  },
};
</script>
  
  <style>
.user-form {
  width: 40%;
  margin: auto;
}
</style>
  