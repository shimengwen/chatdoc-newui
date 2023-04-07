<script setup>
import { onBeforeMount, ref, watch, onMounted, nextTick } from "vue";
import { fetchDcoList, fetchQuery, fetchMsg, fetchDelDoc } from "./api";
import { ElMessage } from "element-plus";
import { Typed } from "@duskmoon/vue3-typed-js";
import Upload from "./views/Upload.vue";
import Loading from "@/assets/loading.svg?component";
import { docState, formatByteSize, docType, nameWithoutExt, docUrl, showLastMessage } from "./utils";
import { DeleteFilled } from "@element-plus/icons-vue";
import AddLink from "./views/AddLink.vue";

var docList = ref([]);
var active = ref(null);
const isHomepage = ref(true);

const options = {
  strings: ["通过 AI 技术和任意文档进行对话", "轻松查阅所需要点", "快速·高效·灵活"],
  loop: true,
  typeSpeed: 150,
  fadeOutDelay: 50,
  backDelay: 2000,
  backSpeed: 50,
  startDelay: 1200,
};

watch(active, () => {
  loadMsg();
});

function loadDos() {
  fetchDcoList().then((res) => {
    if (res) {
      docList.value = res?.data || [];

      if (active.value == null && docList.value.length > 0) {
        const doc = docList.value[0];
        active.value = doc;
      }
    }
  });
}

var messages = ref([]);
var askErr = ref();
function loadMsg() {
  fetchMsg(active.value.doc_id).then((res) => {
    if (res.code) {
      askErr.value = res.message;
      ElMessage.error(res.message);
    } else {
      messages.value = res.data || [];
    }
    showLastMessage(1000);
  });
}

onBeforeMount(() => {
  loadDos();
});

const uploadSuccess = () => {
  loadDos();
  loadFileState();
  isHomepage.value = false;
  F;
};

const changeDoc = (doc) => {
  active.value = doc;
};

var msgLoading = ref(false);
var input = ref("");
function query() {
  if (!input.value) {
    return;
  }
  messages.value.push({
    content: input.value,
    role: "user",
  });
  showLastMessage();
  msgLoading.value = true;
  fetchQuery(active.value.doc_id, input.value).then((res) => {
    messages.value.push({
      content: res?.data?.response || "",
      role: "chatdoc",
    });
    showLastMessage();
    msgLoading.value = false;
  });
  input.value = "";
}

const refInput = ref(null);
const getFocus = () => {
  nextTick(() => {
    if (refInput.value) {
      refInput.value.focus();
    }
  });
};
onMounted(() => {
  getFocus();
});

function delDoc(doc_id) {
  if (active.value.doc_id == doc_id) {
    active.value = null;
  }
  if (active.value == null && docList.value.length > 0) {
    const doc = docList.value[0];
    active.value = doc;
  }

  fetchDelDoc(doc_id).then(() => {
    loadDos();
  });
}

const loadFileState = () => {
  if (docList.value.filter((e) => e.state != 2).length > 0) {
    setTimeout(() => {
      loadDos();
      loadFileState();
    }, 2000);
  }
};

const onerror = (e) => {
  console.log(e);
};
</script>

<template>
  <div class="app-page">
    <div class="bg-center"></div>
    <div class="bg-top"></div>
    <div class="bg-bottom"></div>
    <div class="homepage" v-if="isHomepage">
      <el-row :gutter="32" class="my-el-row">
        <el-col :span="24">
          <header>
            <Typed :options="options">
              <h1 class="typing"></h1>
            </Typed>
          </header>
        </el-col>
        <el-col :span="18" :offset="3">
          <el-card border>
            <Upload :drag="true" @upload-success="uploadSuccess">
              <template #link>
                <AddLink @upload-success="uploadSuccess" />
              </template>
            </Upload>
          </el-card>
        </el-col>
        <el-col :span="24"> </el-col>
        <el-col :xs="{ span: 18 }" :sm="{ span: 18 }" :md="{ span: 6 }" :lg="{ span: 6 }" :xl="{ span: 6 }" :offset="3">
          <el-card>
            <template #header>
              <strong> For Students </strong>
            </template>
            <el-text class="mx-1" tag="p">
              Enhance your learning experience with ChatPDF. Comprehend textbooks, handouts, and presentations
              effortlessly. Don't spend hours flipping through research papers and academic articles.
            </el-text>
            <el-text class="mx-1" tag="p">
              Support your academic growth and succeed in your studies effectively and responsibly.
            </el-text>
          </el-card>
        </el-col>
        <el-col
          :xs="{ span: 18, offset: 3 }"
          :sm="{ span: 18, offset: 3 }"
          :md="{ span: 6, offset: 0 }"
          :lg="{ span: 6, offset: 0 }"
          :xl="{ span: 6, offset: 0 }"
        >
          <el-card>
            <template #header>
              <strong> For Students </strong>
            </template>
            <el-text class="mx-1" tag="p">
              Enhance your learning experience with ChatPDF. Comprehend textbooks, handouts, and presentations
              effortlessly. Don't spend hours flipping through research papers and academic articles.
            </el-text>
            <el-text class="mx-1" tag="p">
              Support your academic growth and succeed in your studies effectively and responsibly.
            </el-text>
          </el-card>
        </el-col>
        <el-col
          :xs="{ span: 18, offset: 3 }"
          :sm="{ span: 18, offset: 3 }"
          :md="{ span: 6, offset: 0 }"
          :lg="{ span: 6, offset: 0 }"
          :xl="{ span: 6, offset: 0 }"
        >
          <el-card>
            <template #header>
              <strong> For Students </strong>
            </template>
            <el-text class="mx-1" tag="p">
              Enhance your learning experience with ChatPDF. Comprehend textbooks, handouts, and presentations
              effortlessly. Don't spend hours flipping through research papers and academic articles.
            </el-text>
            <el-text class="mx-1" tag="p">
              Support your academic growth and succeed in your studies effectively and responsibly.
            </el-text>
          </el-card>
        </el-col>
        <el-col :span="24">
          <div class="copyright">copyright @ 2023</div>
        </el-col>
      </el-row>
    </div>
    <div class="container" v-else>
      <el-container>
        <el-aside class="left-aside" width="300px">
          <div style="flex: 1; overflow-y: auto">
            <div style="padding: 20px">
              <div class="add-doc">
                <Upload @upload-success="uploadSuccess">
                  <template #link>
                    <AddLink @upload-success="uploadSuccess" />
                  </template>
                </Upload>
              </div>
              <div class="doc-list" v-for="item in docList" :key="item.doc_id">
                <div
                  :class="{
                    'doc-item': true,
                    ellipsis: true,
                    'doc-active': item.doc_id === active.doc_id,
                  }"
                  :title="item.doc_name"
                  @click="changeDoc(item)"
                >
                  <div class="doc-item-title">
                    <span style="display: inline-block; width: 70%; overflow: hidden; text-overflow: ellipsis">{{
                      nameWithoutExt(item.doc_name)
                    }}</span>
                    <el-icon @click.stop="delDoc(item.doc_id)" class="remove-icon">
                      <DeleteFilled />
                    </el-icon>
                  </div>
                  <div class="doc-item-info">
                    <span class="doc-item-info-cell">type: {{ docType(item) }}</span>
                    <span class="doc-item-info-cell">size: {{ formatByteSize(item.size || 0) }}</span>
                    <span class="doc-item-info-cell">index: {{ docState(item.state) }}</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="user-info">
            <el-avatar :size="50" src="https://cube.elemecdn.com/3/7c/3ea6beec64369c2642b92c6726f1epng.png" />
            <span class="user-name">Anonymous</span>
          </div>
        </el-aside>
        <el-main>
          <el-row :gutter="32" flex class="my-main">
            <el-col :xs="24" :sm="24" :md="14" :lg="14" :xl="14">
              <div class="doc">
                <div v-if="docList.length == 0" class="empty-info">
                  <div style="color: #333">当前没有文档, 请先上传</div>
                </div>
                <iframe
                  v-if="docList.length > 0 && active"
                  :src="docUrl(active)"
                  style="width: 100%; height: 100%"
                  :key="active.doc_id"
                  @onerror="onerror"
                ></iframe>
              </div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="10" :lg="10" :xl="10">
              <div class="chat">
                <div id="messages" class="messages">
                  <div
                    :class="{
                      'message-item': true,
                      'message-user': item.role == 'user',
                    }"
                    v-for="(item, index) in messages"
                    :key="index"
                  >
                    {{ item.content }}
                  </div>
                </div>
                <div class="loading">
                  <el-icon :size="30" v-if="msgLoading">
                    <Loading />
                  </el-icon>
                </div>
                <div class="input-box">
                  <input
                    ref="refInput"
                    class="input"
                    :placeholder="active?.state != 2 ? '索引构建中...' : '开始与你的文档对话吧'"
                    :disabled="active?.state != 2 || msgLoading"
                    v-model="input"
                    @keyup.up.enter="query"
                  />
                </div>
              </div>
            </el-col>
          </el-row>
        </el-main>
      </el-container>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.my-el-row {
  .el-col {
    margin-bottom: 32px;
  }
}
.app-page {
  position: relative;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}
.mx-1 {
  margin-bottom: 10px;
  line-height: 1.5;
}
.homepage {
  position: relative;
  width: 100%;
  height: 100vh;
  overflow-x: hidden;
  overflow-y: auto;
  .copyright {
    padding: 20px;
    text-align: center;
  }
  header {
    min-height: 66px;
    margin: 36px 0 48px;
    text-align: center;
  }
  :deep(.typed-cursor) {
    font-size: 46px;
    color: rgba(0, 0, 0, 0.4);
  }

  h1 {
    display: inline-block;
    font-weight: 600;
    font-size: 46px;
    text-align: center;
    padding: 0;
    margin: 0;
    margin-bottom: 12px;
  }
}
.bg-center {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: url(./assets/mark-bg.png) center center/cover;
}
.bg-top {
  position: absolute;
  top: -335px;
  left: -170px;
  width: 900px;
  padding-top: 720px;
  background: url(./assets/bg-top.png) no-repeat center center;
  background-size: 100%;
  animation: bgTop 15s linear 1.5s infinite alternate;
}
.bg-bottom {
  position: absolute;
  top: 30%;
  left: -30px;
  width: 120%;
  padding-top: 70%;
  background: url(./assets/bg-bottom.png) no-repeat left center;
  background-size: 100%;
  animation: bgBottom 40s linear infinite alternate;
}

@keyframes bgTop {
  0% {
    top: -335px;
    left: -170px;
  }
  100% {
    top: -463px;
    left: -241px;
  }
}
@keyframes bgBottom {
  0% {
    top: 30%;
    left: -30px;
  }
  100% {
    top: 44%;
    left: -55px;
  }
}
.ellipsis {
  width: 300px;
  overflow: hidden;
  /*文本不会换行*/
  white-space: nowrap;
  /*当文本溢出包含元素时，以省略号表示超出的文本*/
  text-overflow: ellipsis;
}

.empty-info {
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
  color: #cdd0d6;
  font-size: 18px;
  align-items: center;
  justify-content: center;
}

.container {
  position: absolute;
  top: 0;
  left: 0;
  display: flex;
  width: 100%;
  height: 100vh;

  .sidebar {
    display: flex;
    width: 200px;
    width: 15%;
    flex-direction: column;
    justify-content: space-between;

    .add-doc {
      display: flex;
      padding: 10px;
      align-items: center;
      justify-content: space-between;
    }

    .doc-list {
      display: flex;
      align-items: center;
      justify-content: center;

      &:last-child .doc-item {
        border-bottom: none;
      }

      .doc-item {
        width: 100%;
        padding: 3px 10px;
        display: flex;
        flex-direction: column;
        border-bottom: 1px solid gray;

        .doc-item-title {
          display: flex;
          font-size: 14px;
          height: 28px;
          line-height: 28px;
          justify-content: space-between;

          .remove-icon {
            color: #cdd0d6;
            height: 100%;

            &:hover {
              color: #79bbff;
            }
          }
        }

        .doc-item-info {
          display: flex;
          font-size: 10px;
          color: gray;
          height: 10px;
          line-height: 10px;
          justify-content: space-between;

          .doc-item-info-cell {
            margin: 0 2px;
          }
        }
      }

      .doc-active {
        background-color: #a0cfff;
      }
    }
  }
  .user-info {
    padding: 20px;
    display: flex;
    align-items: center;
    background: #1e1d1d;

    .user-name {
      margin: 0 10px;
      color: #cdd0d6;
      font-size: 14px;
    }
  }

  .doc {
    height: 100%;
    background: rgba(84, 17, 211, 0.09);
    border-radius: 6px;
  }

  .chat {
    height: 100%;
    border-radius: 6px;
    background: rgba(84, 17, 211, 0.09);
    flex-direction: column;
    justify-content: space-between;
    display: flex;

    .messages {
      display: flex;
      flex-direction: column;
      padding: 10px;
      overflow-y: scroll;

      .message-item {
        border: 1px solid #cdd0d6;
        padding: 10px;
        margin: 5px 0;
      }

      .message-user {
        background-color: #a0cfff;
      }
    }

    .input {
      display: block;
      padding: 10px;
      height: 48px;
      line-height: 48px;
      border: 1px solid blue;
      background-image: url("assets/send.svg");
      background-repeat: no-repeat;
      background-position: 10px;
      padding-left: 40px;
      border-radius: 6px;
    }

    .loading {
      display: flex;
      align-items: center;
      justify-content: center;
    }
  }
}
.left-aside {
  display: flex;
  flex-direction: column;
  background-color: rgb(0, 21, 41);
}
.container {
  > .el-container {
    height: 100%;
  }
}
.my-main {
  height: 100%;
}
.input-box {
  display: flex;
  padding: 10px;
  background: rgba(255, 255, 255, 1);
  color: #333;
  border: 4px solid rgba(84, 17, 211, 0.09);
  border-radius: 6px;
  box-shadow: 0 0 2px 4px rgba(0, 0, 0, 0.04);
  > input {
    width: 100%;
  }
}
</style>
