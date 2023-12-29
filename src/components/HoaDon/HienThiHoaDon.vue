<template>
  <div>
    <h2>Danh sách hoá đơn</h2>
    <div>
      <label for="sortOrder">Sắp xếp theo:</label>
      <select v-model="selectedSortOption" @change="sortOrders">
        <option value="default">Mặc định</option>
        <option value="price-asc">Giá tăng dần</option>
        <option value="price-desc">Giá giảm dần</option>
        <option value="time-asc">Thời gian tăng dần</option>
        <option value="time-desc">Thời gian giảm dần</option>
      </select>
    </div>
    <table>
      <thead>
        <tr>
          <th>STT</th>
          <th>Thời Gian</th>
          <th>Khách Hàng</th>
          <th>Nhân Viên Bán Hàng</th>
          <th>Tên Cửa Hàng</th>
          <th>Thanh Toán</th>
          <th>Chi Tiết Sản Phẩm</th>
          <th>Tổng Tiền</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(order, index) in orders" :key="order.id">
          <td>{{ index + 1 }}</td>
          <td>{{ formatDateTime(order.orderTime) }}</td>
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
    sortOrders() {
      if (this.selectedSortOption === "price-asc") {
        this.sortOrdersByPrice(1);
      } else if (this.selectedSortOption === "price-desc") {
        this.sortOrdersByPrice(-1);
      } else if (this.selectedSortOption === "time-asc") {
        this.sortOrdersByTime(1);
      } else if (this.selectedSortOption === "time-desc") {
        this.sortOrdersByTime(-1);
      } else {
        // Sắp xếp mặc định
        this.orders.sort((a, b) => a.id - b.id);
      }
    },
    sortOrdersByPrice(sortOrder) {
      this.orders.sort((a, b) => {
        const totalA = this.calculateTotal(a.selectedPhones);
        const totalB = this.calculateTotal(b.selectedPhones);
        return (totalA - totalB) * sortOrder;
      });
    },
    sortOrdersByTime(sortOrder) {
      this.orders.sort((a, b) => {
        const timeA = new Date(a.orderTime).getTime();
        const timeB = new Date(b.orderTime).getTime();
        return (timeA - timeB) * sortOrder;
      });
    },
    calculateTotal(phones) {
      // Tính tổng giá trị của các điện thoại
      return phones.reduce((total, phone) => total + phone.phonePrice, 0);
    },
    formatCurrency(value) {
      // Sử dụng Intl.NumberFormat để định dạng giá tiền
      const formatter = new Intl.NumberFormat("vi-VN", {
        //style: "currency",
        currency: "VND",
      });
      return formatter.format(value);
    },
    formatDateTime(dateTime) {
      // Format thời gian
      if(dateTime==null) return null;
      const options = { year: "numeric", month: "numeric", day: "numeric", hour: "numeric", minute: "numeric" };
      return new Intl.DateTimeFormat("vi-VN", options).format(new Date(dateTime));
    },
  },
};
</script>
  
  <style>
/* Thêm CSS theo ý muốn */
</style>
  