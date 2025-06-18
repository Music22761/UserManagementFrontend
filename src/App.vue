<script setup>
import Table from "./components/Table.vue";
import AddDialog from "./components/AddDialog.vue";
import { ref } from "vue";
import emitter from "./eventBus.js";

const showDialog = ref(false);
const reloadTable = ref(false);

emitter.on("user-changed", () => {
  reloadTable.value = !reloadTable.value;
});
</script>

<template>
  <div class="div1">
    <h1>ระบบการจัดการผู้ใช้งาน</h1>
    <el-button type="primary" @click="showDialog = true">เพิ่มผู้ใช้งาน</el-button>
    <Table :reload="reloadTable" />
    <AddDialog v-model="showDialog" />
  </div>
</template>

<style scoped>
.div1 {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
</style>