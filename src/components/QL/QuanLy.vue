<template>
    <div>
      <header class="app-header">
        <h1>Quản lý cửa hàng</h1>
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
                showHienThiHoaDon = !showHienThiHoaDon;
              "
            >
              Danh sách hoá đơn
            </li>
            <li
              @click="
                resetShow();
                showQLTTNhanVien = !showQLTTNhanVien;
              "
            >
              Quản lý User
            </li>
            <li>Thống kê</li>
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
      <div v-if="showQLTTNhanVien">
        <QuanLyTTNV />
      </div>
      <div v-if="showHienThiHoaDon">
        <HienThiHoaDon />
      </div>
    </div>
  </template>
  
  <script>
  import SanPham from "@/components/NV/SanPham.vue";
  import QuanLyNCC from "@/components/NCC/QuanLyNCC.vue";
  import QuanLyTTNV from "@/components/NV/QLTTNhanVien.vue";
  import HienThiHoaDon from "@/components/HoaDon/HienThiHoaDon.vue";
  export default {
    components: { SanPham, QuanLyNCC, QuanLyTTNV, HienThiHoaDon },
    data() {
      return {
        showSanPham: true,
        showNCC: false,
        showQLTTNhanVien: false,
        showHienThiHoaDon: false,
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
      resetShow() {
        this.showSanPham = false;
        this.showNCC = false;
        this.showQLTTNhanVien = false;
        this.showHienThiHoaDon = false;
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
  