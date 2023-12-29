<template>
  <div>
    <h2>Quản lý Khách Hàng</h2>
    <div>
      <label for="sdt">Tìm kiếm theo SĐT:</label>
      <input v-model="searchSdt" id="sdt" />
      <button class="load-all-btn" @click="searchKhachHang">Tìm kiếm</button>
      <button class="load-all-btn" @click="loadAllKhachHangs">
        Tất cả khách hàng
      </button>
    </div>
   
    <div>
      <h3>{{ editing ? "Sửa Khách Hàng" : "Thêm Khách Hàng" }}</h3>
      <form @submit.prevent="saveKhachHang" class="form-container">
        <div class="form-group">
          <label for="ten">Tên:</label>
          <input v-model="formData.ten" id="ten" required />
        </div>
        <div class="form-group">
          <label for="sdt">SĐT:</label>
          <input v-model="formData.sdt" id="sdt" required />
        </div>
        <div class="form-group">
          <label for="gioitinh">Giới Tính:</label>
          <select v-model="formData.gioitinh" id="gioitinh">
            <option value="">                </option>
            <option value="Nam">Nam</option>
            <option value="Nữ">Nữ</option>
          </select>
        </div>
        <div class="form-group">
          <label for="diachi">Địa Chỉ:</label>
          <input v-model="formData.diachi" id="diachi" />
        </div>
        <button type="submit">{{ editing ? "Cập nhật" : "Thêm mới" }}</button>
      </form>
    </div>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Tên</th>
          <th>SĐT</th>
          <th>Giới Tính</th>
          <th>Địa Chỉ</th>
          <th>Thao Tác</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="khachHang in khachHangs" :key="khachHang.id">
          <td>{{ khachHang.id }}</td>
          <td>{{ khachHang.ten }}</td>
          <td>{{ khachHang.sdt }}</td>
          <td>{{ khachHang.gioitinh }}</td>
          <td>{{ khachHang.diachi }}</td>
          <td>
            <button class="edit-btn" @click="editKhachHang(khachHang)">
              Sửa
            </button>
            <button class="delete-btn" @click="deleteKhachHang(khachHang.id)">
              Xóa
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
  
  <script>
import axios from "axios";

export default {
  data() {
    return {
      khachHangs: [],
      searchSdt: "",
      formData: {
        ten: "",
        sdt: "",
        gioitinh: "",
        diachi: "",
      },
      editing: false,
    };
  },
  methods: {
    fetchKhachHangs() {
      axios.get("http://localhost:8081/api/v1/KH/getAll").then((response) => {
        this.khachHangs = response.data;
      });
    },
    searchKhachHang() {
      axios
        .get(`http://localhost:8081/api/v1/KH/searchBySdt/${this.searchSdt}`)
        .then((response) => {
          if (response.data) {
            this.khachHangs = [response.data];
          }
        })
        .catch((error) => {
          console.error("Error searching for KhachHang:", error);
        });
    },
    saveKhachHang() {
      if (this.editing) {
        axios
          .put(
            `http://localhost:8081/api/v1/KH/edit/${this.formData.id}`,
            this.formData
          )
          .then(() => {
            this.fetchKhachHangs();
            this.resetForm();
          });
      } else {
        axios
          .post("http://localhost:8081/api/v1/KH/save", this.formData)
          .then(() => {
            this.fetchKhachHangs();
            this.resetForm();
          });
      }
    },
    editKhachHang(khachHang) {
      this.formData = { ...khachHang };
      this.editing = true;
    },
    deleteKhachHang(id) {
      axios.delete(`http://localhost:8081/api/v1/KH/delete/${id}`).then(() => {
        this.fetchKhachHangs();
      });
    },
    loadAllKhachHangs() {
      axios
        .get("http://localhost:8081/api/v1/KH/getAll")
        .then((response) => {
          this.khachHangs = response.data;
        })
        .catch((error) => {
          console.error("Error loading all KhachHangs:", error);
        });
    },
    resetForm() {
      this.formData = {
        ten: "",
        sdt: "",
        gioitinh: "",
        diachi: "",
      };
      this.editing = false;
    },
  },
  created() {
    this.fetchKhachHangs();
  },
};
</script>

<style scoped>
.edit-btn {
  background-color: #4caf50; /* Màu xanh lá cây */
  color: white;
  border: none;
  padding: 8px 16px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 14px;
  margin: 4px 2px;
  cursor: pointer;
}

.delete-btn {
  background-color: #f44336; /* Màu đỏ */
  color: white;
  border: none;
  padding: 8px 16px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 14px;
  margin: 4px 2px;
  cursor: pointer;
}

.load-all-btn {
  background-color: #008cba; /* Màu xanh dương */
  color: white;
  border: none;
  padding: 8px 16px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 14px;
  margin: 4px 2px;
  cursor: pointer;
}
.form-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.form-group {
  margin-bottom: 15px;
}

label {
  display: block;
  margin-bottom: 5px;
}

input {
  width: 100%;
  padding: 8px;
  box-sizing: border-box;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-top: 5px;
  margin-bottom: 10px;
}

button {
  background-color: #008cba;
  color: white;
  border: none;
  padding: 10px 15px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #00557e;
}
</style>