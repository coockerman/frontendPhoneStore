<template>
  <div>
    <header class="app-header">
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
              ShowThemHoaDon = !ShowThemHoaDon;
            "
          >
            Thêm hoá đơn
          </li>
          <li
            @click="
              resetShow();
              showKhachHang = !showKhachHang;
            "
          >
            Quản lý khách hàng
          </li>
          <li @click="logout">Đăng xuất</li>
        </ul>
      </nav>
    </header>
    <div v-if="showNCC">
      <QuanLyNCC />
    </div>
    <div v-if="ShowThemHoaDon">
      <ThemHoaDon />
    </div>
    <div v-if="showSanPham">
      <SanPham />
    </div>
    <div v-if="showKhachHang">
      <KhachHang />
    </div>
  </div>
</template>

<script>
import SanPham from "@/components/NV/SanPham.vue";
import ThemHoaDon from "@/components/HoaDon/ThemHoaDon.vue";
import QuanLyNCC from "@/components/NCC/QuanLyNCC.vue";
import KhachHang from "@/components/KhachHang/KhachHang.vue";
export default {
  components: { SanPham, ThemHoaDon, QuanLyNCC, KhachHang },
  data() {
    return {
      ShowThemHoaDon: false,
      showSanPham: true,
      showNCC: false,
      showKhachHang: false,
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
    logout() {
      this.$emit("logout");
    },
    loginSuccess() {
      this.showModal = false;
    },
    loginFailure(error) {
      alert(error);
    },
    resetShow() {
      this.ShowThemHoaDon = false;
      this.showSanPham = false;
      this.showNCC = false;
      this.showKhachHang = false;
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
