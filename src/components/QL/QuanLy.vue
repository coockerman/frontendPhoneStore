<template>
    <div>
      <header class="app-header">
        <h1>Quản lý cửa hàng</h1>
        <nav>
          <ul>
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
            <li
              @click="
                resetShow();
                showHienThiBieuDo = !showHienThiBieuDo;
              "
            >
              Thống kê
            </li>
            
          </ul>
          <div @click="logout" class="logout-button">Đăng xuất</div>
        </nav>
      </header>
      
      <div v-if="ShowThemHoaDon">
        <ThemHoaDon />
      </div>
      
      <div v-if="showQLTTNhanVien">
        <QuanLyTTNV />
      </div>
      <div v-if="showHienThiHoaDon">
        <HienThiHoaDon />
      </div>
      <div v-if="showHienThiBieuDo">
        <BieuDo />
      </div>
    </div>
  </template>
  
  <script>
  import QuanLyTTNV from "@/components/NV/QLTTNhanVien.vue";
  import HienThiHoaDon from "@/components/HoaDon/HienThiHoaDon.vue";
  import BieuDo from "@/components/BieuDo/ChartComponent.vue";
  export default {
    components: { QuanLyTTNV, HienThiHoaDon, BieuDo },
    data() {
      return {
        showQLTTNhanVien: false,
        showHienThiHoaDon: false,
        showHienThiBieuDo: false,
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
        this.showQLTTNhanVien = false;
        this.showHienThiHoaDon = false;
        this.showHienThiBieuDo = false;
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
  .logout-button {
  background-color: #e74c3c;
  color: #fff;
  padding: 10px 15px;
  max-width: 100px;
  margin-left: auto;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.logout-button:hover {
  background-color: #c0392b;
  text-decoration: underline;
}
  </style>
  