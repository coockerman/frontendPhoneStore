<template>
    <div class="user-list">
      <h2>Danh sách User</h2>
      <table>
        <thead>
          <tr>
            <th>STT</th>
            <th>Tên đăng nhập</th>
            <th>Họ tên</th>
            <th>Địa chỉ</th>
            <th>Số điện thoại</th>
            <th>Chức vụ</th>
            <th>Giới tính</th>
            <th>Thao tác</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(user, index) in users" :key="user.id">
            <td>{{ index + 1 }}</td>
            <td>{{ user.userName }}</td>
            <td>{{ user.ten }}</td>
            <td>{{ user.diaChi }}</td>
            <td>{{ user.sdt }}</td>
            <td>{{ user.chucVu }}</td>
            <td>{{ user.gioiTinh }}</td>
            <td>
              <button class="btnEdit" @click="editUser(user)">Edit</button>
              <button class="btnDelete" @click="deleteUser(user.id)">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        users: [],
      };
    },
    mounted() {
      // Tải danh sách người dùng khi component được khởi tạo
      this.getUsers();
    },
    methods: {
      async getUsers() {
        try {
          // Gửi yêu cầu HTTP để lấy danh sách người dùng
          const response = await axios.get('http://localhost:8081/api/v1/user/getAll');
          this.users = response.data;
        } catch (error) {
          console.error('Error fetching users:', error);
        }
      },
      async editUser(user) {
        // Gửi sự kiện "edit-user" với dữ liệu người dùng cần sửa
        this.$emit('edit-user', user);
      },
      async deleteUser(userId) {
        try {
          // Gửi yêu cầu HTTP để xóa người dùng
          await axios.delete(`http://localhost:8081/api/v1/user/delete/${userId}`);
  
          // Tải lại danh sách người dùng sau khi xóa
          this.getUsers();
        } catch (error) {
          console.error('Error deleting user:', error);
        }
      },
    },
  };
  </script>
  
  <style>
  /* ... (đoạn mã CSS tương tự như trong UserList của bạn) ... */
  </style>
  