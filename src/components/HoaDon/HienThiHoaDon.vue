<template>
  <div>
    <h2>Danh sách đơn hàng</h2>
    <table>
      <thead>
        <tr>
          <th>ID Hoá Đơn</th>
          <th>Khách Hàng</th>
          <th>Nhân Viên Bán Hàng</th>
          <th>Tên Cửa Hàng</th>
          <th>Thanh Toán</th>
          <th>Chi Tiết Sản Phẩm</th>
          <th>Tổng Tiền</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="order in orders" :key="order.id">
          <td>{{ order.id }}</td>
          <td>{{ order.customerName }}</td>
          <td>{{ order.salespersonName }}</td>
          <td>{{ order.storeName }}</td>
          <td>{{ order.paymentMethod }}</td>
          <td>
            <ul>
              <li v-for="phone in order.selectedPhones" :key="phone.id">
                {{ phone.phoneName }} - {{ formatCurrency(phone.phonePrice) }} VNĐ
              </li>
            </ul>
          </td>
          <td>{{ formatCurrency(calculateTotal(order.selectedPhones)) }} VNĐ</td>
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
      orders: [],
    };
  },
  mounted() {
    // Gọi API để lấy danh sách đơn hàng khi component được tải
    this.getAllOrders();
  },
  methods: {
    getAllOrders() {
      // Gửi yêu cầu HTTP để lấy danh sách đơn hàng
      axios
        .get("http://localhost:8081/api/orders/getAll")
        .then((response) => {
          this.orders = response.data;
        })
        .catch((error) => {
          console.error("Error fetching orders:", error);
        });
    },
    calculateTotal(phones) {
      // Tính tổng giá trị của các điện thoại
      return phones.reduce((total, phone) => total + phone.phonePrice, 0);
    },
    formatCurrency(value) {
      // Sử dụng Intl.NumberFormat để định dạng giá tiền
      const formatter = new Intl.NumberFormat("vi-VN", {
        style: "currency",
        currency: "VND",
      });
      return formatter.format(value);
    },
  },
};
</script>
  
  <style>
/* Thêm CSS theo ý muốn */
</style>
  