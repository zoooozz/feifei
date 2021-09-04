<template>
  <div class="executions">
    <!--执行命令-->
    <p
      class="code"
      v-for="(execution, index) in executions"
      :key="index"
      v-show="execution.visible"
    >
      <span class="addon">~</span>
      [<span class="time">{{ execution.time }}</span
      >]
      <span class="task">{{ execution.name }}</span>
      <span class="duration" v-if="execution.duration !== undefined"
        >{{ execution.duration }} ms</span
      >
    </p>
    <!--进度条-->
    <p class="code" v-show="isProcessed">
      <span class="addon">~</span>
      {{ progressBarText }}
      <span class="percentage">{{ percentage }}%</span>
    </p>
    <!--执行命令-->
    <p class="code" v-show="endExecution.visible">
      <span class="addon">~</span>
      [<span class="time">{{ endExecution.time }}</span
      >]
      <span class="task">{{ endExecution.name }}</span>
    </p>
  </div>
</template>

<script>
import data from "../mock/data";
export default {
  name: "Executions",
  props: ["canExecute"],
  data() {
    return {
      executions: data.executions,
      isProcessed: false,
      isFinished: false,
      progressBarText: "--------------------------",
      endExecution: {
        name: "打开中...",
        time: "",
        visible: false,
      },
    };
  },
  computed: {
    percentage() {
      const hashL = this.progressBarText
        .split("")
        .filter((c) => c === "#").length;
      const l = this.progressBarText.length;
      return Math.floor((hashL * 100) / l);
    },
  },
  watch: {
    canExecute: function (val) {
      if (val === true) {
        this.runExecutions();
      }
    },
  },
  updated() {
    this.$emit("onUpdating");
  },
  methods: {
    // 逐条执行命令
    runExecutions: async function () {
      // 执行最后一条命令
      setTimeout(() => {
        this.isFinished = true;
        this.$emit("onFinish");
      }, 500);
    },
  },
};
</script>

<style lang="less">
.executions {
  .addon {
    color: #68fcfb;
  }
  .time {
    color: #666;
  }
  .task {
    color: #009ab2;
  }
  .duration {
    margin-left: 10px;
    color: #bf36b7;
  }
}
</style>