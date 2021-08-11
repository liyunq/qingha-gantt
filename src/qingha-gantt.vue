<template>
  <div>
    <div class="table task-father">
      <div class="table-left">
        <div class="row" style="height: 80px">
          <!-- 左边头部 -->
          <div
            class="item header"
            style="height: 80px; border-top: 1px solid #dfe6ec"
            v-for="(item, index) in columns"
            :key="index"
            :style="'width:' + item.width + 'px'"
          >
            <SelfDiv
              :value="item.label"
              :renderTemp="item.labelrender"
            ></SelfDiv>
            <!-- {{item.labelrender($createElement) }} -->
            <!-- {{ item.label }} -->
            <!-- {{label}} -->
          </div>
        </div>
        <!-- 左边body -->
        <div
          class="table-body"
          style="overflow-y: hidden; overflow-x: hidden"
          ref="tablebodycolumns"
        >
          <div class="row" v-for="(info, infoindex) in table" :key="infoindex">
            <div
              class="item"
              v-for="(item, index) in columns"
              :key="index"
              :style="'width:' + item.width + 'px'"
            >
              <SelfDiv
                :value="info[item.value]"
                :renderTemp="item.render"
                :item="{ row: info, index: infoindex }"
              ></SelfDiv>
            </div>
          </div>
          <div class="row" style="height: 20px"></div>
        </div>
      </div>

      <div class="table-right" ref="tableright" @scroll="tablerightx($event)">
        <!-- 右边头部 -->
        <div class="table-right-header" ref="tablerightheader">
          <div
            class="item header"
            :style="
              'width:' +
              (60 * days + 20) +
              'px;' +
              'border-top: 1px solid #dfe6ec;'
            "
          >
            {{ title }}
          </div>
          <div class="row">
            <div v-for="n in days" :key="n" class="item header">
              {{ n + "号" }}
            </div>
          </div>
        </div>
        <!-- 右边body -->
        <div
          class="table-body"
          ref="tablebodytask"
          @scroll="tablebodytasky($event)"
        >
          <div
            class="row task-father"
            v-for="(info, infoindex) in table"
            :key="infoindex"
          >
            <div class="item" v-for="n in days" :key="n"></div>
            <!-- 右边task任务 -->
            <template v-if="info.tasks">
              <div
                class="task"
                @click="tasksItemClick(info, item, infoindex, index)"
                v-for="(item, index) in info.tasks"
                :style="
                  'background:' +
                  (getColor == null ? '#4C4C4C' : getColor(item)) +
                  ';left:' +
                  ((item.start - 1) * 60 + 8) +
                  'px;width:' +
                  ((item.end - item.start) * 60 + 44) +
                  'px;'
                "
                :key="item.label"
              >
                <!-- 'background:' + (getColor==null?'#4C4C4C':getColor(item)) +  -->
                <div
                  class="task-progress"
                  v-if="item.progress"
                  :class="
                    item.progress == item.end ? 'all-radius' : 'half-radius'
                  "
                  :style="
                    'left:' +
                    ((item.start - 1) * 60 + 8) +
                    'px;width:' +
                    ((item.progress - item.start) * 60 + 44) +
                    'px;'
                  "
                ></div>
                <div class="task">{{ item.label }}</div>
              </div>
            </template>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import SelfDiv from "./self-div.vue";

export default {
  name: "QinghaGantt",
  components: {
    SelfDiv,
  },
  props: {
    table: {
      default: [],
      type: Array,
    },
    columns: {
      default: [],
      type: Array,
    },
    title: {
      default: "",
      type: String,
    },
    days: {
      default: 31,
      type: Number,
    },
    getColor: {
      default: null,
      type: Function,
    },
  },
  data() {
    return {};
  },
  created() {},
  methods: {
    tablebodytasky(event) {
      this.$refs.tablebodycolumns.scrollTop =
        this.$refs.tablebodytask.scrollTop;
      this.$refs.tablerightheader.scrollLeft =
        this.$refs.tablebodytask.scrollLeft;
    },
    tablerightx(event) {
      this.$refs.tablebodytask.scrollLeft = this.$refs.tableright.scrollLeft;
    },
    tasksItemClick(info, item, infoindex, index) {
      this.$emit("tasksItemClick",info, item, infoindex, index);
    },
  },
  watch: {},
};
</script>
<style lang="less" scoped>
.task-father {
  transform: translateX(0px);
  .fixed {
    position: fixed;
    z-index: 99;
  }
}

.task-father {
  transform: translateX(0px);

  .task {
    position: fixed;

    z-index: 99;
    height: 26px;
    top: 7px;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 15px;

    font-size: 10px;
    color: #ffffff;

    &:active {
      opacity: 0.7;
    }

    .task-progress {
      position: fixed;
      height: 26px;

      background: linear-gradient(
        45deg,
        rgba(255, 255, 255, 0.3) 0,
        rgba(255, 255, 255, 0.3) 25%,
        transparent 25%,
        transparent 50%,
        rgba(255, 255, 255, 0.3) 50%,
        rgba(255, 255, 255, 0.3) 75%,
        transparent 75%,
        transparent
      );
      background-size: 30px 30px;

      &.all-radius {
        border-radius: 15px;
      }
      &.half-radius {
        border-radius: 15px 0px 0px 15px;
      }
    }
  }
}

.table {
  display: flex;

  .table-right {
    max-width: 50%;
  }
  .table-body {
    height: 500px;
    overflow: scroll;
  }

  .table-right-header {
    width: 100%;
    overflow: scroll;
    overflow-x: hidden;
    overflow-y: hidden;
  }
}

.item {
  border-bottom: 1px solid #dfe6ec;
  border-right: 1px solid #dfe6ec;
  height: 40px;
  width: 60px;
  display: inline-block;
  min-width: 60px;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #606266;

  font-size: 13px;
}

.row {
  display: flex;
  height: 40px;
}
.header {
  color: #909399;
  font-weight: 700;
  background: #f5f7fa;
}

::-webkit-scrollbar {
  width: 20px;
  height: 20px;
}

::-webkit-scrollbar-track,
::-webkit-scrollbar-thumb {
  border-radius: 999px;
  border: 5px solid transparent;
}

::-webkit-scrollbar-track {
  box-shadow: 1px 1px 5px rgba(0, 0, 0, 0.2) inset;
}

::-webkit-scrollbar-thumb {
  min-height: 20px;
  background-clip: content-box;
  box-shadow: 0 0 0 5px rgba(0, 0, 0, 0.2) inset;
}

::-webkit-scrollbar-corner {
  background: transparent;
}
</style>
