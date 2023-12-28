<template>
  <div class="app-container">
    <h1>Quản lý Nhà Cung Cấp</h1>
    <div class="container">
      <div class="form-container in-container">
        <!-- Form thêm/sửa Nhà Cung Cấp -->
        <form @submit.prevent="submitForm" class="my-form">
          <!-- Các trường dữ liệu -->
          <div class="form-group">
            <label for="ten">Tên Nhà Cung Cấp:</label>
            <input v-model="nhaCungCap.ten" type="text" id="ten" required maxlength="20" />
          </div>

          <div class="form-group">
            <label for="sdt">Số Điện Thoại:</label>
            <input v-model="nhaCungCap.sdt" type="text" id="sdt" required maxlength="12"/>
          </div>

          <div class="form-group">
            <label for="diachi">Địa Chỉ:</label>
            <input
              v-model="nhaCungCap.address"
              type="text"
              id="diachi"
              required
              maxlength="40"
            />
          </div>

          <!-- Nút thêm/sửa -->
          <div class="form-group">
            <button type="submit">
              {{ editing ? "Cập nhật" : "Thêm mới" }}
            </button>
          </div>
        </form>
      </div>

      <!-- Danh sách Nhà Cung Cấp -->
      <div class="list-container in-container">
        <h2>Danh sách Nhà Cung Cấp</h2>
        <table>
          <thead>
            <tr>
              <th>Tên Nhà Cung Cấp</th>
              <th>Số Điện Thoại</th>
              <th>Địa Chỉ</th>
              <th>Thao Tác</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="nhaCungCap in nhaCungCaps" :key="nhaCungCap.id">
              <td>{{ nhaCungCap.ten }}</td>
              <td>{{ nhaCungCap.sdt }}</td>
              <td>{{ nhaCungCap.address }}</td>
              <td>
                <button @click="editNhaCungCap(nhaCungCap)" class="edit-btn">
                  Sửa
                </button>
                <button
                  @click="deleteNhaCungCap(nhaCungCap.id)"
                  class="delete-btn"
                >
                  Xóa
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>
  
  <script>
import axios from "axios";

export default {
  data() {
    return {
      nhaCungCaps: [],
      nhaCungCap: {
        ten: "",
        sdt: "",
        address: "",
      },
      editing: false,
    };
  },
  mounted() {
    this.loadNhaCungCaps();
  },
  methods: {
    loadNhaCungCaps() {
      axios
        .get("http://localhost:8081/api/v1/NCC/getAll")
        .then((response) => {
          this.nhaCungCaps = response.data;
        })
        .catch((error) => {
          console.error("Error fetching data: ", error);
        });
    },
    submitForm() {
      if (this.editing) {
        // Nếu đang sửa, gửi request cập nhật
        axios
          .put(
            `http://localhost:8081/api/v1/NCC/edit/${this.nhaCungCap.id}`,
            this.nhaCungCap
          )
          .then(() => {
            this.loadNhaCungCaps();
            this.clearForm();
          })
          .catch((error) => {
            console.error("Error updating NhaCungCap: ", error);
          });
      } else {
        // Nếu không phải sửa, gửi request thêm mới
        axios
          .post("http://localhost:8081/api/v1/NCC/save", this.nhaCungCap)
          .then(() => {
            this.loadNhaCungCaps();
            this.clearForm();
          })
          .catch((error) => {
            console.error("Error adding NhaCungCap: ", error);
          });
      }
    },
    editNhaCungCap(nhaCungCap) {
      // Chuyển sang chế độ sửa và điền dữ liệu
      this.editing = true;
      this.nhaCungCap = { ...nhaCungCap };
    },
    deleteNhaCungCap(id) {
      // Gửi request xóa
      axios
        .delete(`http://localhost:8081/api/v1/NCC/delete/${id}`)
        .then(() => {
          this.loadNhaCungCaps();
          this.clearForm();
        })
        .catch((error) => {
          console.error("Error deleting NhaCungCap: ", error);
        });
    },
    clearForm() {
      // Xóa dữ liệu và chuyển về chế độ thêm mới
      this.nhaCungCap = {
        ten: "",
        sdt: "",
        diachi: "",
      };
      this.editing = false;
    },
  },
};
</script>

<style>
/* CSS cho giao diện */
.app-container {
  width: 100%;
  align-items: center;
}

.container {
  width: 100%;
  display: flex;
  justify-content: space-between;
}

.in-container {
    margin: 20px 80px;
}
.my-form {
  max-width: 400px;
  width: 100%;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
}

button {
  background-color: #4caf50;
  color: white;
  padding: 10px 15px;
  border: none;
  border-radius: 3px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #45a049;
}

.list-container {
  width: 60%;
  margin-top: 20px;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 10px;
}

thead th {
  background-color: #4caf50;
  color: white;
  padding: 10px;
  text-align: left;
}

tbody tr {
  border-bottom: 1px solid #ddd;
}

td {
  padding: 10px;
}

/* Hiệu ứng cho nút "Sửa" và "Xóa" */
button.edit-btn {
  background-color: rgb(43, 200, 218);
  margin-right: 5px;
  transition: background-color 0.3s ease;
}
button.delete-btn {
  background-color: rgb(255, 87, 34);
  margin-right: 5px;
  transition: background-color 0.3s ease;
}

button.edit-btn:hover,
button.delete-btn:hover {
  background-color: #1e87f0;
}
</style>
  