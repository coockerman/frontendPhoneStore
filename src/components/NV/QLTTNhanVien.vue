<template>
    <div id="app">
      <user-form ref="userForm" @user-updated="getUsers" />
      <user-list ref="userList" @edit-user="handleEditUser" />
    </div>
  </template>
  
  <script>
  import UserForm from '@/components/NV/UserForm.vue';
  import UserList from '@/components/NV/UserList.vue';
  
  export default {
    components: {
      UserForm,
      UserList,
    },
    methods: {
      getUsers() {
        // Gọi hàm để tải lại danh sách người dùng
        this.$refs.userList.getUsers();
      },
      handleEditUser(user) {
        // Gọi hàm để đưa dữ liệu người dùng cần sửa vào form
        this.$refs.userForm.editUser(user);
      },
    },
  };
  </script>
  
  <style>
  /* ... (đoạn mã CSS tương tự như trong App của bạn) ... */
  </style>
  