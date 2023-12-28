<template>
  <div>
    <div class="phone-manager">
      <div class="phone-form">
        <h2>Thêm/sửa sản phẩm</h2>
        <div class="input-container">
          <label for="phoneName">Tên:</label>
          <input
            v-model="phoneName"
            id="phoneName"
            type="text"
            maxlength="26"
          />
        </div>
        <div class="input-container">
          <label for="phoneType">Loại:</label>
          <input
            v-model="phoneType"
            id="phoneType"
            type="text"
            maxlength="18"
          />
        </div>
        <div class="input-container">
          <label for="phoneColor">Màu sắc:</label>
          <input
            v-model="phoneColor"
            id="phoneColor"
            type="text"
            maxlength="12"
          />
        </div>

        <div class="input-container">
          <label for="ncc">Nhà cung cấp:</label>
          <select v-model="idNCC" id="ncc">
            <option v-for="NCC in NCCs" :key="NCC.id" :value="NCC.id">
              {{ NCC.ten }}
            </option>
          </select>
        </div>

        <div class="input-container">
          <label for="phonePrice">Giá:</label>
          <input
            v-model="phonePrice"
            id="phonePrice"
            type="number"
            @input="limitNumberLength"
          />
        </div>
        <button @click="savePhone">Lưu</button>

        <!-- Hiển thị thông báo lỗi -->
        <div v-if="error" class="error-message">
          {{ error }}
        </div>
        <div class="input-container search-container">
          <label for="searchName">Tìm Kiếm Theo Tên:</label>
          <input v-model="searchName" id="searchName" type="text" />
          <button class="btnSearch" @click="searchPhones">Tìm Kiếm</button>
        </div>
      </div>

      <div class="phone-list">
        <h2>Danh sách sản phẩm</h2>
        <!-- sort -->
        <div class="sort-options">
          <label for="sortBy"></label>
          <select v-model="selectedSortOption" @change="sortPhones">
            <option
              v-for="option in sortOptions"
              :key="option.value"
              :value="option.value"
            >
              {{ option.label }}
            </option>
          </select>
        </div>

        <!-- table -->
        <table>
          <thead>
            <tr>
              <th>STT</th>
              <th>Tên</th>
              <th>Loại</th>
              <th>Màu sắc</th>
              <th>NCC</th>
              <th>Giá</th>
              <th>Thao tác</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(phone, index) in phones" :key="phone._id">
              <td>{{ index + 1 }}</td>
              <td>{{ phone.phoneName }}</td>
              <td>{{ phone.phoneType }}</td>
              <td>{{ phone.phoneColor }}</td>
              <td>{{ phone.ncc.ten }}</td>
              <td>{{ formatCurrency(phone.phonePrice) }}</td>
              <td>
                <button class="btnEdit" @click="editPhone(phone)">Edit</button>
                <button class="btnDelete" @click="deletePhone(phone._id)">
                  Delete
                </button>
              </td>
            </tr>
          </tbody>
        </table>
        <h2>Kết Quả Tìm Kiếm</h2>
        <table v-if="searchResults.length > 0">
          <thead>
            <tr>
              <th>STT</th>
              <th>Tên</th>
              <th>Loại</th>
              <th>Màu sắc</th>
              <th>NCC</th>
              <th>Giá</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(phone, index) in searchResults" :key="phone._id">
              <td>{{ index + 1 }}</td>
              <td>{{ phone.phoneName }}</td>
              <td>{{ phone.phoneType }}</td>
              <td>{{ phone.phoneColor }}</td>
              <td>{{ phone.ncc.ten }}</td>
              <td>{{ formatCurrency(phone.phonePrice) }}</td>
            </tr>
          </tbody>
        </table>

        <div v-else>
          <p>Không có kết quả tìm kiếm.</p>
        </div>
      </div>
    </div>
  </div>
</template>
  
  <script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      phones: [],
      phoneName: "",
      phoneType: "",
      phoneColor: "",
      idNCC: 0,
      selectedNCC: null,
      NCCs: [],
      phonePrice: 0,
      formattedPhonePrice: "0",
      editingPhoneId: null,
      error: null,
      searchName: "", // Thêm biến lưu tên cần tìm
      searchResults: [], // Mảng lưu kết quả tìm kiếm
      sortOptions: [
        { value: "null", label: "Không sắp xếp" },
        { value: "name-asc", label: "Tên A->Z" },
        { value: "name-desc", label: "Tên Z->A" },
        { value: "price-asc", label: "Giá tăng dần" },
        { value: "price-desc", label: "Giá giảm dần" },
      ],
      selectedSortOption: "null",
    };
  },
  mounted() {
    this.getPhones();
    this.loadNCC();
  },
  methods: {
    async getPhones() {
      try {
        const response = await axios.get(
          "http://localhost:8081/api/v1/student/getAllPhone"
        );
        this.phones = response.data;
        this.sortPhones();
        this.searchPhones();
      } catch (error) {
        console.error("Error fetching phones:", error);
      }
    },
    formatCurrency(value) {
      // Sử dụng Intl.NumberFormat để định dạng giá tiền
      const formatter = new Intl.NumberFormat("vi-VN", {
        style: "currency",
        currency: "VND",
      });
      return formatter.format(value);
    },
    limitNumberLength() {
      // Giới hạn độ dài của số là 10 ký tự
      if (this.phonePrice.toString().length > 10) {
        // Nếu độ dài vượt quá 10, cắt giảm độ dài
        this.phonePrice = parseFloat(this.phonePrice.toString().slice(0, 12));
      }
    },
    findNCCById() {
      // Sử dụng find để tìm NCC trong mảng NCCs
      this.selectedNCC = this.NCCs.find((ncc) => ncc.id === this.idNCC);
    },
    loadNCC() {
      axios
        .get("http://localhost:8081/api/v1/NCC/getAll")
        .then((response) => {
          this.NCCs = response.data;
        })
        .catch((error) => {
          console.error("Error fetching data: ", error);
        });
    },
    async sortPhones() {
      if (this.selectedSortOption === "name-asc") {
        this.sortPhonesByName(1);
      } else if (this.selectedSortOption === "name-desc") {
        this.sortPhonesByName(-1);
      } else if (this.selectedSortOption === "price-asc") {
        this.sortPhonesByPrice(1);
      } else if (this.selectedSortOption === "price-desc") {
        this.sortPhonesByPrice(-1);
      } else {
        return;
      }
    },
    async sortPhonesByPrice(sortOrder) {
      if (sortOrder === 0) {
        return; // Không sắp xếp nếu là lựa chọn mặc định
      }

      this.phones.sort((a, b) => {
        return (a.phonePrice - b.phonePrice) * sortOrder;
      });
    },
    async sortPhonesByName(sortOrder) {
      if (sortOrder === 0) {
        return; // Không sắp xếp nếu là lựa chọn mặc định
      }

      this.phones.sort((a, b) => {
        const nameA = a.phoneName.toUpperCase();
        const nameB = b.phoneName.toUpperCase();
        if (nameA < nameB) {
          return -sortOrder;
        }
        if (nameA > nameB) {
          return sortOrder;
        }
        return 0;
      });
    },
    resetSortOrder() {
      this.sortOrder = 0;
    },
    async savePhone() {
      this.findNCCById();
      // Kiểm tra nếu ô không được nhập nội dung
      if (
        !this.phoneName ||
        !this.phoneType ||
        !this.phoneColor ||
        this.selectedNCC == null ||
        this.phonePrice < 0
      ) {
        this.error = "Vui lòng nhập đầy đủ";
        return;
      }

      // Kiểm tra nếu giá tiền không đúng định dạng
      if (
        isNaN(this.phonePrice) ||
        !Number.isInteger(parseFloat(this.phonePrice))
      ) {
        this.phonePrice = 0;
      }

      const phoneData = {
        phoneName: this.phoneName,
        phoneType: this.phoneType,
        phoneColor: this.phoneColor,
        ncc: this.selectedNCC,
        phonePrice: this.phonePrice,
      };

      try {
        if (this.editingPhoneId) {
          await axios.put(
            `http://localhost:8081/api/v1/student/edit/${this.editingPhoneId}`,
            phoneData
          );
        } else {
          await axios.post(
            "http://localhost:8081/api/v1/student/save",
            phoneData
          );
        }

        this.resetForm();
        this.getPhones();
      } catch (error) {
        console.error("Error saving phone:", error);
      }
    },
    async searchPhones() {
      if (this.searchName == "") {
        this.searchResults = [];
        return;
      }
      try {
        // Gọi API tìm kiếm theo tên
        const response = await axios.get(
          `http://localhost:8081/api/v1/student/search/${this.searchName}`
        );
        this.searchResults = response.data;
      } catch (error) {
        console.error("Error searching phones:", error);
      }
    },
    async editPhone(phone) {
      this.phoneName = phone.phoneName;
      this.phoneType = phone.phoneType;
      this.phoneColor = phone.phoneColor;
      this.selectedNCC = phone.ncc;
      this.phonePrice = phone.phonePrice;
      this.editingPhoneId = phone._id;
    },
    async deletePhone(phoneId) {
      try {
        await axios.delete(
          `http://localhost:8081/api/v1/student/delete/${phoneId}`
        );
        this.getPhones();
      } catch (error) {
        console.error("Error deleting phone:", error);
      }
    },
    resetForm() {
      this.phoneName = "";
      this.phoneType = "";
      this.phoneColor = "";
      this.selectedNCC = null;
      this.phonePrice = 0;
      this.editingPhoneId = null;
      this.error = "";
      this.idNCC = 0;

    },
  },
};
</script>
  
  <style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.app-header {
  background-color: #3794a5;
  color: #fff;
  padding: 15px;
  text-align: center;
}

.app-header h1 {
  margin: 0;
}

.app-header nav ul {
  list-style: none;
  padding: 0;
  display: flex;
  justify-content: center;
}

.app-header nav li {
  margin-right: 20px;
  cursor: pointer;
}

.app-header nav li:hover {
  text-decoration: underline;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

table,
th,
td {
  border: 1px solid #ddd;
}

th,
td {
  padding: 10px;
  text-align: left;
}

th {
  background-color: #f2f2f2;
}
.btnEdit {
  background-color: rgb(43, 200, 218);
  color: white;
  padding: 8px 12px;
  margin-right: 2px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.btnEdit:hover {
  background-color: #2ba8b8;
}
.btnDelete {
  background-color: rgb(255, 87, 34);
  color: white;
  padding: 8px 12px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.btnDelete:hover {
  background-color: #ff5722;
}

/* Phần Add/Edit Phone */
.phone-form {
  max-width: 400px;
  margin: 0 auto;
  background-color: #fff;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.input-container {
  margin-bottom: 16px;
}

label {
  display: block;
  margin-bottom: 8px;
  font-weight: bold;
  text-align: left;
  padding-left: 10px;
}

input {
  width: calc(100% - 16px);
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
  font-size: 16px;
}

button {
  background-color: #2b85af;
  color: #fff;
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
}

.btnSearch {
  margin-top: 10px;
}

button:hover {
  background-color: #2b85af;
}

/* Hiển thị thông báo lỗi */
.error-message {
  color: #e74c3c;
  margin-top: 10px;
}
.phone-manager {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}
.phone-list {
  flex: 1;
}

/* Phần Add/Edit Phone */
.phone-form {
  width: 40%; /* Điều chỉnh chiều rộng theo ý muốn */
  float: left; /* Đặt float để nằm bên trái */
  background-color: #fff;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}
@media (max-width: 768px) {
  /* Điều chỉnh layout khi độ rộng màn hình nhỏ hơn hoặc bằng 768px */
  .phone-manager {
    flex-direction: column;
  }
}
.search-container {
  margin-top: 25px;
}
/* Thêm kiểu cho list box */
select {
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  cursor: pointer;
  outline: none; /* Loại bỏ đường viền khi focus */
}

/* Thêm kiểu cho option trong list box */
select option {
  background-color: #fff;
  color: #333;
}

/* Thêm kiểu cho option được chọn */
select option:checked {
  background-color: #3498db;
  color: #fff;
}

/* Thêm kiểu cho option khi hover */
select option:hover {
  background-color: #3498db;
  color: #fff;
}
th,
td {
  padding: 10px;
  padding-right: 4px;
  text-align: left;
}

th {
  background-color: #f2f2f2;
}

/* Thêm kiểu cho cột số thứ tự */
td:first-child {
  font-weight: bold;
}
</style>
  