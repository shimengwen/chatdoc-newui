<template>
  <el-upload
    ref="uploadRef"
    :class="drag ? 'drag-upload' : 'simple-upload'"
    :drag="drag"
    action="/api/upload"
    :auto-upload="true"
    :show-file-list="false"
    :on-success="uploadSuccess"
    :before-upload="beforeUpload"
  >
    <el-icon v-if="drag" class="el-icon--upload"><UploadFilled color="#409eff" /></el-icon>
    <div v-if="drag" class="el-upload__text">拖拽 PDF 文件到此处，或<em>点击此处进行上传</em></div>
    <template v-if="drag" #tip>
      <slot name="link" />
    </template>
    <template v-if="!drag" #trigger>
      <div class="trigger-text">
        <p>+ 新建会话</p>
        <p>点击上传文件</p>
      </div>
    </template>
  </el-upload>
</template>
<script setup>
import { UploadFilled } from "@element-plus/icons-vue";
const emit = defineEmits(["uploadSuccess"]);
const props = defineProps({
  drag: {
    type: Boolean,
    default: false,
  },
});
const { drag, type } = props;
const supportFileType = [
  "application/pdf",
  "application/epub+zip",
  "text/markdown",
  "text/plain",
  "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
];
const beforeUpload = (rawFile) => {
  if (!supportFileType.includes(rawFile.type)) {
    console.log(rawFile.type);
    ElMessage.error("仅支持 .pdf, .epub, .md, .txt 类型文件");
    return false;
  } else if (rawFile.size / 1024 / 1024 > 2) {
    ElMessage.error("文件大小不能超过 2MB!");
    return false;
  }
  return true;
};

const uploadSuccess = () => {
  emit("uploadSuccess");
};
</script>
<style lang="less">
.simple-upload {
  padding: 10px;
  background: rgba(255, 255, 255, 0.12);
  border-radius: 6px;
  border: 1px dashed #d9d9d9;
  .el-upload--text {
    display: block;
  }
  .trigger-text {
    color: #fff;
    text-align: center;
    display: block;
    font-size: 12px;
    font-weight: 400;
    line-height: 1.5;
    p {
      margin: 0;
    }
  }
}
</style>
