<script setup>
import EditDialog from "./EditDialog.vue";
import axios from "axios";
import { ref, onMounted, watch } from "vue";
import emitter from "../eventBus.js";
import UserIcon from "../assets/user_icon.png";
import { ElMessage, ElMessageBox } from "element-plus";

const props = defineProps({ reload: Boolean });
const users = ref([]);
const showEditDialog = ref(false);
const selectedUser = ref(null);

const fetchUsers = async () => {
  try {
    const response = await axios.get("http://localhost:3000/users");
    users.value = response.data;
  } catch (error) {
    console.error("โหลดข้อมูลผิดพลาด:", error);
  }
};

function editUser(user) {
  selectedUser.value = user;
  showEditDialog.value = true;
}

async function deleteUser(user) {
  try {
    await ElMessageBox.confirm(
      `คุณต้องการลบผู้ใช้ "${user.name}" ใช่หรือไม่?`,
      "ยืนยันการลบ",
      {
        confirmButtonText: "ลบ",
        cancelButtonText: "ยกเลิก",
        type: "warning",
      }
    );

    await axios.delete(`http://localhost:3000/users/${user.id}`);

    ElMessage({
      type: "success",
      message: "ลบผู้ใช้สำเร็จ",
    });

    await fetchUsers();
  } catch (error) {
    if (error !== "cancel") {
      ElMessage({
        type: "error",
        message: "เกิดข้อผิดพลาดในการลบ",
      });
    }
  }
}

onMounted(fetchUsers);
watch(() => props.reload, fetchUsers);
</script>

<template>
  <el-table :data="users" style="width: 70%">
    <el-table-column label="" width="60">
      <template #default>
        <img
          :src="UserIcon"
          alt="User Icon"
          style="width: 30px; height: 40px"
        />
      </template>
    </el-table-column>
    <el-table-column prop="id" label="รหัสสมาชิก" width="100" />
    <el-table-column prop="name" label="ชื่อ" width="180" />
    <el-table-column prop="email" label="อีเมล" />
    <el-table-column label="จัดการ">
      <template #default="scope">
        <el-button type="warning" @click="editUser(scope.row)">แก้ไข</el-button>
        <el-button type="danger" @click="deleteUser(scope.row)"
          >ลบข้อมูล</el-button
        >
      </template>
    </el-table-column>
  </el-table>
  <EditDialog v-model="showEditDialog" :user="selectedUser" />
</template>