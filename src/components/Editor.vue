<template>
  <div class="wedding-editor" ref="editor">
    <!-- 打开邀请函 -->
    <iframe
      src="//player.bilibili.com/player.html?aid=335149131&bvid=BV1aA411c7Gq&cid=401100630&page=1"
      scrolling="no"
      border="0"
      frameborder="no"
      framespacing="0"
      allowfullscreen="true"
    >
    </iframe>

    <div
      class="editor-open"
      v-if="(canStart || hasClosed) && !canOpen"
      @click="canOpen = true"
    >
      <svg
        viewBox="0 0 24 24"
        width="18"
        height="18"
        stroke="currentColor"
        stroke-width="2"
        fill="none"
        stroke-linecap="round"
        stroke-linejoin="round"
        class="css-i6dzq1"
      >
        <rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect>
        <line x1="9" y1="3" x2="9" y2="21"></line>
      </svg>
    </div>
    <Executions
      :canExecute="canExecute"
      @onUpdating="scrollToBottom"
      @onFinish="canOpen = true"
    />
    <invitation
      :canOpen="canOpen"
      @onClose="(canOpen = false), (hasClosed = true)"
      @sendBarrage="onAfterSending"
    />
  </div>
</template>

<script>
import Prism from "prismjs";
import "prismjs/themes/prism-okaidia.css";
import "../utils/raf";
import data from "../mock/data";

import Executions from "./Executions";
import Invitation from "./Invitation";

export default {
  name: "Editor",
  components: { Executions, Invitation },
  data() {
    return {
      startDate: "",
      code: data.code,
      currentCode: "",
      isCursorVisible: 1,
      canExecute: false,
      canOpen: false,
      wish: "",
      hasClosed: false,
      canStart: false,
    };
  },
  created() {
    this.startDate = new Date().toDateString();
    this.progressivelyTyping();
  },
  updated() {
    this.scrollToBottom();
  },
  computed: {
    highlightedCode() {
      const code = Prism.highlight(
        this.currentCode,
        Prism.languages.javascript
      );
      const codeWithCursor = `${code}<span style="opacity:${this.isCursorVisible}"> ▍</span>`;
      return codeWithCursor;
    },
  },
  methods: {
    scrollToBottom() {
      // 保持页面一直滚到最下面
      this.$refs.editor.scrollTop = 100000;
    },
    // 代码输入
    progressivelyTyping() {
      setTimeout(() => {
        this.canExecute = true;
        this.$emit("onFinish");
      }, 5000);
    },
    // 发送弹幕之后
    onAfterSending(wish) {
      this.wish = wish;
      this.canOpen = false;
      setTimeout(() => {
        this.canStart = true;
      }, 800);
    },
  },
};
</script>

<style lang="less">
iframe {
  height: 100%;
  width: 100%;
}
.wedding-editor {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow-x: hidden;
  overflow-y: auto;
  z-index: 1;
  transform-origin: 0 0;
  -webkit-transform-origin: 0 0;
  transition: all 1.6s cubic-bezier(0.4, 0, 1, 1);
  -webkit-transition: all 1.6s cubic-bezier(0.4, 0, 1, 1);
  .editor-header {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    padding: 12px;
    overflow: hidden;
    background: #2b2b48;
    z-index: 3;
    > a {
      float: left;
      display: block;
      width: 16px;
      height: 16px;
      margin-right: 5px;
      border-radius: 8px;
      background: #fc615d;
      &.minimum {
        background: #fdbc40;
      }
      &.maximum {
        background: #34c84a;
      }
    }
  }
  p.code {
    margin: 0;
    color: #bbb;
    line-height: 1.2;
    font-family: "Roboto Mono", "Menlo", "Monaco", Courier, monospace !important;
    font-weight: 500 !important;
    font-size: 16px !important;
  }
  pre {
    margin: 0;
    white-space: pre-wrap;
    code {
      white-space: pre-wrap;
      word-break: break-all;
      font-size: 16px !important;
      margin: 0;
      color: #bbb;
      line-height: 1.2;
      font-family: "Roboto Mono", "Menlo", "Monaco", Courier, monospace !important;
      font-weight: 500 !important;
      background: transparent;
    }
  }
  .editor-open {
    position: fixed;
    right: 20px;
    bottom: 20px;
    width: 40px;
    height: 40px;
    padding: 6px;
    border-radius: 20px;
    text-align: center;
    line-height: 18px;
    border: 5px solid #ffd69b;
    color: #a9895d;
    background: #fff1de;
    z-index: 1000;
  }
}
</style>