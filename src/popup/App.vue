<template>
  <div id="app" class="app">
    <div class="header">
      <p class="title" :style="{color:color}">chrome extension Demo</p>
      <el-color-picker
        v-model="color"
        size="mini"
        show-alpha
        :predefine="predefineColors"
        @change="handleColorChanage"
      />
    </div>
    <div class="body" :style="{backgroundColor:color}">
      <el-button type="primary" @click="deleteHistory">删除历史记录</el-button>
    </div>
  </div>
</template>

<style lang="less">
.app {
  width: 600px;
  .header {
    height: 30px;
    margin: 10px;
    .el-color-picker {
      float: right;
      margin: 1px;
    }
    .title {
      float: left;
      height: 30px;
      line-height: 30px;
      font-size: 26px;
      font-weight: bolder;
      transition: color 0.5s;
    }
  }
  .body {
    min-height: 370px;
    padding: 10px;
    transition: background 0.5s;
  }
}
.v-modal {
  background: rgba(0, 0, 0, 0.2);
}
</style>

<script>
import ajax from "@fdaciuk/ajax";
import moment from "moment";
export default {
  data() {
    return {
      color: "rgba(0, 0, 0, 0.5)",
      predefineColors: [
        "rgba(0, 0, 0, 0.5)",
        "#ff8c00",
        "#ffd700",
        "#90ee90",
        "rgba(255, 69, 0, 0.68)",
        "rgb(255, 120, 0)",
        "hsv(51, 100, 98)",
        "hsva(120, 40, 94, 0.5)",
        "hsla(181, 100%, 37%,  0.73)",
        "#c7158577"
      ]
    };
  },
  methods: {
    deleteHistory() {
      this.$confirm("此操作将删除所有历史记录, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          chrome.history.deleteAll(result => {
            this.$message({
              type: "success",
              message: "删除成功!"
            });
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },
    handleColorChanage(color) {
      // 保存主题信息  
      chrome.storage.sync.set({ theme: color }, res => {
        console.log("颜色保存成功");
      });
    }
  },
  created() {
    // 读取 主题信息
    chrome.storage.sync.get("theme", ({ theme }) => {
      if (!theme) {
        return false;
      }
      this.color = theme;
    });
    // 读取 书签信息
    chrome.bookmarks.getTree(data => {
      console.log(data);
    });
  }
};
</script>
