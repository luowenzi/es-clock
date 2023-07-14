<template>
  <div class="clock">
    <ul>
      <li v-for="(item, idx) in clockArray" :key="idx">
        <p class="time" @click="setClockIndex(idx)">
          {{ item.clock }}: {{ item.minute }}
        </p>
        <p class="message" @click="setClockIndex(idx)">{{ item.message }}</p>
        <el-switch
          v-model="item.status"
          active-color="#13ce66"
          inactive-color="#000000"
        ></el-switch>
      </li>
    </ul>
    <div class="buttons">
      <el-button class="options-btn" @click="confirm(clockArray)">确定</el-button>
    </div>
  </div>
</template>

<script>
import { cloneDeep } from 'lodash'
export default {
  props: {
    clockList: {
      type: Array,
      default: () => [],
    },
    setClockIndex: {
      type: Function,
      default: () => () => {},
    },
    confirm: {
      type: Function,
      default: () => () => {},
    },
  },
  data() {
    return {
        clockArray: []
    }
  },
  watch: {
    clockList: {
        handler(list) {
            this.clockArray = cloneDeep(list)
        },
        immediate: true
    }
  },
};
</script>

<style scoped>
.clock {
  padding: 54px;
}

ul {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  list-style: none;
}

li {
  display: flex;
  align-items: center;
  width: 100%;
  margin-bottom: 10px;
}

p {
  margin-right: 20px;
  color: #fff;
}

.time {
  font-size: 40px;
}

.message {
  font-size: 20px;
}
</style>
