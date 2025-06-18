<script setup>
import { ref, computed, watch } from "vue";
import axios from "axios";
import { ElMessage } from "element-plus";
import emitter from "../eventBus.js";

const props = defineProps({ modelValue: Boolean, user: Object });
const emit = defineEmits(["update:modelValue"]);

const dialogVisible = computed({
  get: () => props.modelValue,
  set: (val) => emit("update:modelValue", val),
});

const form = ref({ id: null, name: "", email: "" });
const formLabelWidth = "140px";

watch(() => props.user, (newUser) => {
  if (newUser) {
    form.value = { ...newUser };
  }
}, { immediate: true });

const close = () => {
  dialogVisible.value = false;
};

const updateUser = async () => {
  try {
    await axios.patch(`http://localhost:3000/users/${form.value.id}`, {
      name: form.value.name,
      email: form.value.email,
    });
    ElMessage.success("อัปเดตสำเร็จ");
    close();
    emitter.emit("user-changed");
  } catch (err) {
    console.error("อัปเดตผิดพลาด", err);
  }
};
</script>

<template>
  <el-dialog v-model="dialogVisible" title="แก้ไขผู้ใช้งาน" width="500">
    <el-form :model="form">
      <el-form-item label="ชื่อ" :label-width="formLabelWidth">
        <el-input v-model="form.name" />
      </el-form-item>
      <el-form-item label="อีเมล" :label-width="formLabelWidth">
        <el-input v-model="form.email" />
      </el-form-item>
    </el-form>
    <template #footer>
      <el-button @click="close">ยกเลิก</el-button>
      <el-button type="primary" @click="updateUser">บันทึก</el-button>
    </template>
  </el-dialog>
</template>