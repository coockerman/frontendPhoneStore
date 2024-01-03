<template>
    <h1>Thống kê doanh thu</h1>
  <!-- <div class="chart-container">
    
  </div> -->
  <div class="chart">
      <Bar id="my-chart-id" :options="chartOptions" :data="chartData7day" />
    </div>
    <div class="chart">
      <Bar id="my-chart-id" :options="chartOptions" :data="chartData30day" />
    </div>
  <div>
    <Bar id="my-chart-id" :options="chartOptions" :data="chartData12month" />
  </div>
</template>
  
  <script>
import axios from "axios";

import { Bar } from "vue-chartjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from "chart.js";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
);

export default {
  name: "BarChart",
  components: { Bar },
  mounted() {
    // Gọi API để lấy danh sách đơn hàng khi component được tải
    this.getAllOrders();
  },
  computed: {
    chartData7day() {
      return {
        labels: this.getLabelByDay(7),
        datasets: [
          {
            label: "Thống kê 7 ngày gần đây",
            data: this.data7day,
            backgroundColor: "rgba(75, 192, 192, 0.2)",
            borderColor: "rgba(75, 192, 192, 1)",
            borderWidth: 1,
          },
        ],
      };
    },
    chartData30day() {
      return {
        labels: this.getLabelByDay(30),
        datasets: [
          {
            label: "Thống kê 30 ngày gần đây",
            data: this.data30day,
            backgroundColor: "rgba(75, 192, 192, 0.2)",
            borderColor: "rgba(75, 192, 192, 1)",
            borderWidth: 1,
          },
        ],
      };
    },
    chartData12month() {
      return {
        labels: this.getLabelByMonth(12),
        datasets: [
          {
            label: "Thống kê theo tháng",
            data: this.data12month,
            backgroundColor: "rgba(75, 192, 192, 0.2)",
            borderColor: "rgba(75, 192, 192, 1)",
            borderWidth: 1,
          },
        ],
      };
    },
    chartOptions() {
      return {
        responsive: true,
      };
    },
  },
  data() {
    return {
      orders: [],
      data7day: [],
      data30day: [],
      data12month: [],
    };
  },
  methods: {
    getAllOrders() {
      // Gửi yêu cầu HTTP để lấy danh sách đơn hàng
      axios
        .get("http://localhost:8081/api/orders/getAll")
        .then((response) => {
          this.orders = response.data;
          this.calculateAndSetChartDataBy7Day();
          this.calculateAndSetChartDataBy30Day();
          this.calculateAndSetChartDataBy12Months();
        })
        .catch((error) => {
          console.error("Error fetching orders:", error);
        });
    },
    calculateAndSetChartDataBy7Day() {
      const allDays = this.getLabelByDay(7);
      const totals = allDays.map((day) => {
        const total = this.orders
          .filter((order) => {
            const orderDate = new Date(order.orderTime).toLocaleDateString(
              "vi-VN",
              { month: "numeric", day: "numeric" }
            );
            return orderDate === day;
          })
          .reduce(
            (sum, order) => sum + this.calculateTotal(order.selectedPhones),
            0
          );
        return total;
      });
      const defaultValues = Array(allDays.length).fill(0);
      this.data7day = totals.length > 0 ? totals.map(Number) : defaultValues;
    },
    calculateAndSetChartDataBy30Day() {
      const allDays = this.getLabelByDay(30);
      const totals = allDays.map((day) => {
        const total = this.orders
          .filter((order) => {
            const orderDate = new Date(order.orderTime).toLocaleDateString(
              "vi-VN",
              { month: "numeric", day: "numeric" }
            );
            return orderDate === day;
          })
          .reduce(
            (sum, order) => sum + this.calculateTotal(order.selectedPhones),
            0
          );
        return total;
      });
      const defaultValues = Array(allDays.length).fill(0);
      this.data30day = totals.length > 0 ? totals.map(Number) : defaultValues;
    },
    calculateAndSetChartDataBy12Months() {
      const allMonths = this.getLabelByMonth(12);
      const totals = allMonths.map((month) => {
        const total = this.orders
          .filter((order) => {
            const orderMonth = new Date(order.orderTime).toLocaleDateString(
              "vi-VN",
              { month: "numeric", year: "numeric" }
            );
            return orderMonth === month;
          })
          .reduce(
            (sum, order) => sum + this.calculateTotal(order.selectedPhones),
            0
          );
        return total;
      });
      const defaultValues = Array(allMonths.length).fill(0);
      this.data12month =
        totals.length > 0 ? totals.map(Number) : defaultValues;
    },
    calculateTotal(phones) {
      // Tính tổng giá trị của các điện thoại
      return phones.reduce((total, phone) => total + phone.phonePrice, 0);
    },
    getLabelByDay(day) {
      const labels = [];
      for (let i = day - 1; i >= 0; i--) {
        const date = new Date();
        date.setDate(date.getDate() - i);
        labels.push(
          date.toLocaleDateString("vi-VN", { month: "numeric", day: "numeric" })
        );
      }
      return labels;
    },
    getLabelByMonth(months) {
      const labels = [];
      const currentDate = new Date();

      for (let i = months; i >= 0; i--) {
        const date = new Date(
          currentDate.getFullYear(),
          currentDate.getMonth() - i,
          1
        );
        labels.push(
          date.toLocaleDateString("vi-VN", {
            month: "numeric",
            year: "numeric",
          })
        );
      }
      return labels;
    },
  },
};
</script>
<style scoped>
/* Định dạng các phần tử ngoại trừ component */
body {
  font-family: "Arial", sans-serif;
  background-color: #f4f4f4;
}
.chart-container {
  display: flex;
  justify-content: space-around; /* Cách nhau một khoảng cách */
}

.chart {
  flex: 1; /* Phần tử sẽ mở rộng để lấp đầy không gian */
  margin: 0 5px; /* Khoảng cách giữa các biểu đồ */
}
/* Định dạng phần tử trong component */
#my-chart-id {
  margin: 20px 0;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  background-color: #fff;
}

/* Định dạng tiêu đề biểu đồ */
.chart-title {
  text-align: center;
  font-size: 18px;
  margin: 10px 0;
}

/* Định dạng chú thích */
.chart-legend {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

/* Định dạng chú thích mỗi mục */
.chart-legend-item {
  display: flex;
  align-items: center;
  margin-right: 20px;
}

/* Định dạng mỗi mục chú thích */
.chart-legend-color {
  width: 20px;
  height: 20px;
  margin-right: 5px;
}

/* Định dạng mô tả mỗi mục chú thích */
.chart-legend-label {
  font-size: 14px;
}
</style>