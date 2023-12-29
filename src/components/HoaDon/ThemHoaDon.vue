<template>
  <div class="form-parent">
    <div class="form-container">
      <div v-if="messageFalseExists" class="notification">
        {{ notificationMessageFalse }}
      </div>
      <div v-if="messageTrueExists" class="notificationTrue">
        {{ notificationMessageTrue }}
      </div>
      <form @submit.prevent="submitForm" class="my-form">
        <div class="form-group">
          <label for="customer">Chọn khách hàng:</label>
          <select
            v-model="selectedCustomer"
            @change="updateCustomerName"
            id="customer"
          >
            <option
              v-for="customer in customers"
              :key="customer.id"
              :value="customer.id"
            >
              {{ customer.ten }} - {{ customer.sdt }}
            </option>
          </select>
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
          <button type="submit" @click="submitForm">Thêm hoá đơn</button>
        </div>
      </form>
    </div>
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
      messageFalseExists: false,
      messageTrueExists: false,
      notificationMessageFalse: "",
      notificationMessageTrue: "",
      customers: [], // Thêm danh sách khách hàng
      selectedCustomer: null,
      selectedCustomerName: "",
    };
  },
  computed: {
    totalAmount() {
      return this.selectedProducts.reduce(
        (total, product) => total + product.phonePrice,
        0
      );
    },
  },
  mounted() {
    this.loadCustomers();
    this.loadProducts();
  },
  methods: {
    loadCustomers() {
      axios
        .get("http://localhost:8081/api/v1/KH/getAll")
        .then((response) => {
          this.customers = response.data;
        })
        .catch((error) => {
          console.error("Error fetching customers: ", error);
        });
    },
    saveData() {
      const dataToSend = {
        customerName: this.selectedCustomerName,
        salespersonName: this.salespersonName,
        storeName: this.storeName,
        paymentMethod: this.paymentMethod,
        selectedPhones: this.selectedProducts,
      };
      this.deletePhoneHadBuy();
      axios
        .post("http://localhost:8081/api/orders/save", dataToSend)
        .then((response) => {
          console.log("Dữ liệu đã được lưu thành công:", response.data);
          this.showNotificationTrue("Lưu đơn hàng thành công");
        })
        .catch((error) => {
          console.error("Lỗi khi lưu dữ liệu:", error);
        });
    },
    async deletePhoneHadBuy() {
      for (const product of this.selectedProducts) {
        await this.deletePhone(product._id);
      }
      this.loadProducts();
    },
    updateCustomerName() {
      const selectedCustomer = this.customers.find(
        (customer) => customer.id === this.selectedCustomer
      );
      if (selectedCustomer) {
        this.selectedCustomerName = selectedCustomer.ten;
      } else {
        this.selectedCustomerName = "";
      }
    },
    loadProducts() {
      axios
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
        !this.selectedCustomer ||
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
      this.selectedCustomer = null;
      this.salespersonName = "";
      this.selectedProducts = [];
      this.storeName = "";
      this.paymentMethod = "cash";
      // Thêm các trường dữ liệu khác nếu cần

      this.selectedProduct = null;
    },
    formatCurrency(amount) {
      return new Intl.NumberFormat("vi-VN", {
        style: "currency",
        currency: "VND",
      }).format(amount);
    },
    showNotification(message) {
      this.notificationMessageFalse = message;
      this.messageFalseExists = true;
      this.messageTrueExists = false;
      // Ẩn thông báo sau 3 giây
      setTimeout(() => {
        this.messageFalseExists = false;
      }, 3000);
    },
    showNotificationTrue(message) {
      this.notificationMessageTrue = message;
      this.messageTrueExists = true;
      this.messageFalseExists = false;
      // Ẩn thông báo sau 3 giây
      setTimeout(() => {
        this.messageTrueExists = false;
      }, 3000);
    },
  },
};
</script>
  
  <style scoped>
/* CSS cho biểu mẫu */
.form-parent {
  margin-top: 20px;
  width: 100%;
}
.form-container {
  display: flex;
  justify-content: center;
  align-items: center;
  /* height: 100vh; */
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
  display: block;
  top: 10px;
  right: 10px;
  background-color: #ff3333;
  color: white;
  padding: 10px;
  border-radius: 5px;
}
.notificationTrue {
  position: fixed;
  display: block;
  top: 10px;
  right: 10px;
  background-color: #22ee74;
  color: white;
  padding: 10px;
  border-radius: 5px;
}
</style>
  