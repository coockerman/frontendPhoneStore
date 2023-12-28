<template>
  <div class="form-container">
    <div v-if="productExists" class="notification">
      {{ notificationMessage }}
    </div>
    <form @submit.prevent="submitForm" class="my-form">
      <div class="form-group">
        <label for="customerName">Tên khách hàng:</label>
        <input v-model="customerName" type="text" id="customerName" required />
      </div>

      <div class="form-group">
        <label for="salespersonName">Tên người bán:</label>
        <input
          v-model="salespersonName"
          type="text"
          id="salespersonName"
          required
        />
      </div>

      <div class="form-group">
        <label for="product">Chọn sản phẩm:</label>
        <div class="product-selection">
          <select v-model="selectedProduct" id="product">
            <option
              v-for="product in availableProducts"
              :key="product._id"
              :value="product._id"
            >
              {{ product.phoneName }}
            </option>
          </select>
          <button type="button" @click="addProduct">Thêm sản phẩm</button>
        </div>
      </div>

      <div class="form-group">
        <label>Danh sách sản phẩm:</label>
        <ul v-if="selectedProducts.length > 0" class="product-list">
          <li v-for="(product, index) in selectedProducts" :key="index">
            {{ product.phoneName }} - {{ formatCurrency(product.phonePrice) }}
            <button type="button" @click="removeProduct(index)">Xóa</button>
          </li>
        </ul>
        <p v-else class="no-products">Không có sản phẩm.</p>
      </div>

      <div class="form-group">
        <label for="storeName">Cơ sở:</label>
        <input v-model="storeName" type="text" id="storeName" required />
        <!-- <select v-model="storeName" id="storeName">
          <option value="HD">Hà Đông</option>
          <option value="HK">Hoàn Kiếm</option>
          <option value="HBT">Hai Bà Trưng</option>
        </select> -->
      </div>

      <div class="form-group">
        <label for="paymentMethod">Hình thức thanh toán:</label>
        <select v-model="paymentMethod" id="paymentMethod">
          <option value="cash">Tiền mặt</option>
          <option value="creditCard">Thẻ tín dụng</option>
          <!-- Thêm các tùy chọn thanh toán khác nếu cần -->
        </select>
      </div>

      <div class="form-group">
        <label for="totalAmount">Tổng tiền:</label>
        <span class="total-amount-value">{{
          formatCurrency(totalAmount)
        }}</span>
      </div>

      <div class="form-group">
        <button type="submit" @click="submitForm">Submit</button>
      </div>
    </form>
  </div>
</template>
  
  <script>
import axios from "axios";
export default {
  data() {
    return {
      // Các trường khác
      availableProducts: [],
      selectedProduct: null,
      selectedProducts: [],
      customerName: "",
      salespersonName: "",
      storeName: "",
      paymentMethod: "cash",
      productExists: false,
      notificationMessage: "",
    };
  },
  computed: {
    availableProductsFiltered() {
      return this.availableProducts.filter((product) => !product.select);
    },
    totalAmount() {
      return this.selectedProducts.reduce(
        (total, product) => total + product.phonePrice,
        0
      );
    },
  },
  mounted() {
    this.loadProducts();
  },
  methods: {
    async saveData() {
      // Chuẩn bị dữ liệu để gửi lên server
      const dataToSend = {
        customerName: this.customerName,
        salespersonName: this.salespersonName,
        // ... thêm các trường dữ liệu khác tương ứng
        storeName: this.storeName,
        paymentMethod: this.paymentMethod,
        selectedPhones: this.selectedProducts,
      };
      for (const product of this.selectedProducts) {
        this.deletePhone(product._id);
      }
      // Gửi HTTP Request đến API của Spring Boot
      await axios
        .post("http://localhost:8081/api/orders/save", dataToSend)
        .then((response) => {
          console.log("Dữ liệu đã được lưu thành công:", response.data);

          // Xử lý sau khi lưu thành công nếu cần
        })
        .catch((error) => {
          console.error("Lỗi khi lưu dữ liệu:", error);
          return;
          // Xử lý lỗi nếu cần
        });

      this.loadProducts();
    },
    async loadProducts() {
      await axios
        .get("http://localhost:8081/api/v1/student/getAllPhone")
        .then((response) => {
          this.availableProducts = response.data;
        })
        .catch((error) => {
          console.error("Error fetching data: ", error);
        });
    },
    addProduct() {
      if (this.selectedProducts.some((p) => p._id === this.selectedProduct)) {
        // Nếu sản phẩm đã tồn tại trong danh sách, hiển thị thông báo và không thêm vào nữa
        this.showNotification("Sản phẩm đã được thêm");
        return;
      }

      if (this.selectedProduct) {
        const product = this.availableProducts.find(
          (p) => p._id === this.selectedProduct
        );
        if (product) {
          this.selectedProducts.push(product);
        }
      }
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
    removeProduct(index) {
      const product = this.selectedProducts[index];
      if (product) {
        this.selectedProducts.splice(index, 1);
      }
    },
    submitForm() {
      if (
        !this.customerName ||
        !this.salespersonName ||
        this.selectedProducts.length === 0 ||
        !this.storeName ||
        !this.paymentMethod
      ) {
        // Nếu không đầy đủ, hiển thị thông báo và không tiến hành submit
        this.showNotification("Vui lòng nhập đầy đủ thông tin!");
        return;
      }
      this.saveData();
      this.clearForm();
    },
    clearForm() {
      // Xóa dữ liệu trong state
      this.customerName = "";
      this.salespersonName = "";
      this.selectedProducts = [];
      this.storeName = "";
      this.paymentMethod = "cash";
      // Thêm các trường dữ liệu khác nếu cần

      // Reset giá trị của selectedProduct để đảm bảo rằng select box trên form cũng được đặt về giá trị mặc định
      this.selectedProduct = null;
    },
    formatCurrency(amount) {
      return new Intl.NumberFormat("vi-VN", {
        style: "currency",
        currency: "VND",
      }).format(amount);
    },
    showNotification(message) {
      // Hiển thị thông báo
      this.notificationMessage = message;
      this.productExists = true;
      // Ẩn thông báo sau 3 giây
      setTimeout(() => {
        this.productExists = false;
      }, 3000);
    },
  },
};
</script>
  
  <style scoped>
/* CSS cho biểu mẫu */
.form-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.my-form {
  max-width: 600px;
  width: 100%;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  text-align: center; /* Đưa nội dung về giữa */
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
}

.product-selection {
  display: flex;
  gap: 10px;
  justify-content: center; /* Đưa phần chọn sản phẩm và nút Thêm sản phẩm vào giữa */
}

button {
  background-color: #4caf50;
  color: white;
  padding: 10px 15px;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}

.product-list {
  list-style: none;
  padding: 0;
  text-align: left; /* Đưa danh sách sản phẩm sang trái */
}

.product-list li {
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  justify-content: space-between; /* Đưa nút Xóa về bên phải */
}

.product-list button {
  background-color: #ff3333;
  color: white;
  padding: 5px 10px;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

.product-list button:hover {
  background-color: #cc0000;
}

.no-products {
  color: #666;
  margin-top: 0;
}

.total-amount-value {
  text-align: left; /* Đưa tổng tiền sang trái */
  font-weight: bold;
  font-size: 1.2em; /* Tăng kích thước font cho tổng tiền */
  display: block; /* Hiển thị tổng tiền dưới dạng block để xuống dòng */
  margin-top: 10px; /* Tăng khoảng cách giữa Tổng tiền và nút Submit */
  margin-left: 50px;
}
.notification {
  position: fixed;
  top: 10px;
  right: 10px;
  background-color: #ff3333;
  color: white;
  padding: 10px;
  border-radius: 5px;
}
</style>
  