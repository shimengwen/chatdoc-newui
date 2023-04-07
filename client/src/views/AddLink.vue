<template>
  <el-text style="display: inline-block; padding-top: 10px; cursor: pointer" type="primary" @click="open"
    >文档链接地址</el-text
  >
</template>

<script lang="ts" setup>
import { ElMessage, ElMessageBox } from "element-plus";
import { Plus } from "@element-plus/icons-vue";
import { fetchAddLink } from "../api";
const emit = defineEmits(["uploadSuccess"]);
const open = () => {
  ElMessageBox.prompt("", "输入链接", {
    inputPlaceholder: "https://example.com/file.pdf",
    confirmButtonText: "确认",
    cancelButtonText: "取消",
    inputPattern: /^(ftp|http|https):\/\/[^ "]+$/,
    inputErrorMessage: "不是一个有效链接",
  })
    .then(({ value }) => {
      fetchAddLink(value).then((res) => {
        emit("uploadSuccess");
      });
    })
    .catch(() => {});
};
</script>
