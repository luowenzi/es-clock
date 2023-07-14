<template>
  <div class="clock">
    <div class="time">
      <input
        type="text"
        v-model="params.clock"
        @change="inpChange('clock')"
        @keyup="maxChange('clock')"
        maxlength="2"
      />
      <span>:</span>
      <input
        type="text"
        v-model="params.minute"
        @change="inpChange('minute')"
        @keyup="maxChange('minute')"
        maxlength="2"
      />
    </div>
    <div class="msg">
      <div class="date">
        <span>标签</span>
        <input type="text" v-model="params.message" />
      </div>
      <div class="label">
        <span>日期</span>
        <span>每天</span>
      </div>
    </div>
    <div class="buttons">
      <div>
        <el-button class="options-btn" @click="setClockFn()">返回</el-button>
      </div>
      <el-button class="options-btn" @click="confirm">确定</el-button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    clockItem: {
      type: Object,
      default: () => ({}),
    },
    setClockFn: {
      type: Function,
      default: () => () => {},
    },
  },
  data() {
    return {
      params: {
        minute: "",
        clock: "",
        message: "",
      },
    };
  },
  watch: {
    clockItem: {
      handler(item) {
        Object.keys(this.params).forEach((key) => {
          this.params[key] = item[key];
        });
      },
      immediate: true,
    },
  },

  methods: {
    maxChange(type) {
      if (this.params[type] > 24 && type == "clock") {
        this.params[type] = `24`;
        return;
      }

      if (this.params[type] > 60 && type == "minute") {
        this.params[type] = `60`;
        return;
      }
    },

    inpChange(type) {
      if (this.params[type] == 0) {
        this.params[type] = `00`;
        return;
      }
      let num = this.params[type].replace(/^0+/, "");
      this.params[type] = this.params[type] < 10 ? `0${num}` : num;
    },
    confirm() {
      if (!this.params.message) {
        this.params.message = "闹钟";
      }
      this.setClockFn(this.params);
    },
  },
};
</script>

<style scoped>
.clock {
  background: #201f1f;
  min-height: calc(100vh - 32px);
  padding: 16px 24px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  position: relative;
}

.time {
  display: flex;
  align-items: center;
  justify-content: center;
}

.time input {
  width: 40px;
  height: 30px;
  font-size: 22px;
  text-align: center;
  outline-style: none;
  border-radius: 4px;
  border: none;
}

.time span {
  color: #fff;
  margin: 0 20px;
}

.msg {
  margin-top: 20px;
}

.msg {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 90%;
  padding: 0 40px;
  background: #353535;
  border-radius: 10px;
  margin-top: 40px;
}

.msg .date {
  border-bottom: 1px solid #535353;
}
.msg .date,
.msg .label {
  width: 90%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 14px 30px;
}

.msg .date span:first-child,
.msg .label span:first-child {
  color: #fff;
}

.msg .date span:last-child,
.msg .label span:last-child {
  color: #929292;
}

.msg input {
  outline-style: none;
  height: 30px;
  font-size: 18px;
  background: transparent;
  border: none;
  color: #929292;
  text-align: right;
}

.btn {
  position: absolute;
  right: 20px;
  bottom: 20px;
  background: #353535;
  outline-style: none;
  border: 1px solid #fff;
  color: #fff;
  padding: 2px 14px;
  border-radius: 4px;
}
</style>
