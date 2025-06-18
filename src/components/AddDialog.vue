<script setup>
import { ref, computed } from "vue";
import axios from "axios";
import { ElMessage } from "element-plus";
import emitter from "../eventBus.js";

const props = defineProps(["modelValue"]);
const emit = defineEmits(["update:modelValue"]);

const dialogVisible = computed({
  get: () => props.modelValue,
  set: (val) => emit("update:modelValue", val),
});

const form = ref({ name: "", email: "" });
const formLabelWidth = "140px";

const close = () => {
  form.value = { name: "", email: "" };
  dialogVisible.value = false;
};

const addUser = async () => {
  try {
    await axios.post("http://localhost:3000/users", form.value);
    ElMessage.success("เพิ่มผู้ใช้สำเร็จ");
    close();
    emitter.emit("user-changed");
  } catch (err) {
    console.error("เพิ่มไม่สำเร็จ", err);
  }
};
</script>

<template>
  <el-dialog v-model="dialogVisible" title="เพิ่มผู้ใช้งาน" width="500">
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
      <el-button type="primary" @click="addUser">เพิ่ม</el-button>
    </template>
  </el-dialog>
</template>