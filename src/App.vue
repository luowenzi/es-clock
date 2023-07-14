<!-- eslint-disable no-debugger -->
<template>
  <div id="app">
    <!-- 路由匹配到的组件将渲染在这里 -->
    <i class="el-icon-setting" @click="openDrawer" v-show="!drawer"></i>
    <i
      class="el-icon-arrow-up"
      @click="changeVideoIndex(-1)"
      v-show="showArrow && videoIndex !== 0"
    ></i>
    <i
      class="el-icon-arrow-down"
      @click="changeVideoIndex(1)"
      v-show="showArrow && videoIndex !== videoList.length - 1"
    ></i>
    <div class="time-clock">
      <span>{{ nowHour }}</span>
      <span>:</span>
      <span>{{ nowMin }}</span>
    </div>
    <div class="bg-videos">
      <div
        :style="{
          transform: `translateY(-${videoIndex * 100}vh)`,
          transition: 'all 2s',
        }"
      >
        <div class="video" v-for="(item, index) in videoList" :key="index">
          <video ref="video" muted loop>
            <source :src="item" type="video/mp4" />
          </video>
        </div>
      </div>
    </div>
    <el-drawer
      :visible.sync="drawer"
      direction="ltr"
      :with-header="false"
      custom-class="setting-drawer"
      :modal="false"
      :before-close="beforeDrawerClose"
    >
      <p class="clock-scene">场景闹钟</p>
      <p class="clock-scene small-scene">| {{ clockScene }}</p>
    </el-drawer>
    <el-drawer
      direction="rtl"
      :visible.sync="clockDrawer"
      :modal="false"
      custom-class="clock-drawer"
      :with-header="false"
      :destroy-on-close="true"
    >
      <el-collapse-transition>
        <div v-show="!isSetClock">
          <Clock
            :clockList="clockList"
            :setClockIndex="setClockIndex"
            :confirm="confirm"
          />
        </div>
      </el-collapse-transition>
      <el-collapse-transition>
        <div v-show="isSetClock">
          <SetClock :clockItem="clockItem" :setClockFn="setClockFn" />
        </div>
      </el-collapse-transition>
    </el-drawer>
    <div class="buttons" v-if="drawer && !clockDrawer">
      <el-button class="options-btn" @click="openClockDrawer">确定</el-button>
    </div>
    <div class="left-button" v-if="clockDrawer && !isSetClock">
      <i class="el-icon-plus" @click="setClockIndex(-1)"></i>
    </div>
  </div>
</template>

<script>
import Clock from "@/components/clock.vue";
import SetClock from "@/components/setClock.vue";
import { cloneDeep } from "lodash";

const clockList = [
  {
    status: false,
    clock: "06",
    minute: "00",
    message: "闹钟",
    videoIndex: 1
  },
];
export default {
  name: "App",
  components: {
    Clock,
    SetClock,
  },
  data() {
    return {
      videoList: [
        require("@/assets/1.mp4"),
        require("@/assets/2.mp4"),
        require("@/assets/3.mp4"),
        require("@/assets/4.mp4"),
        require("@/assets/5.mp4"),
      ],
      drawer: false,
      clockDrawer: false,
      showArrow: false,
      clockScene: "闹钟",
      videoIndex: 0,
      isSetClock: false,
      clockIndex: -1,
      clockItem: cloneDeep(clockList[0]),
      clockList: cloneDeep(clockList),
      lastTimestamp: 0,
      lastRing: null,
      nowHour: "00",
      nowMin: "00",
    };
  },
  mounted() {
    setTimeout(() => {
      window.requestAnimationFrame(this.computeClock);
      this.$confirm("是否打开声音?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "info",
      }).then(() => {
        this.$refs.video[this.videoIndex].play();
        this.$refs.video[this.videoIndex].muted = false;
      });
    }, 3000);
  },
  methods: {
    openDrawer() {
      this.drawer = true;
      this.showArrow = true;
    },
    openClockDrawer() {
      this.clockDrawer = true;
      this.showArrow = false;
      this.$refs.video[this.videoIndex].muted = true;
    },
    changeVideoIndex(val) {
      this.$refs.video[this.videoIndex].muted = true;
      this.$refs.video[this.videoIndex].play();
      this.$refs.video[this.videoIndex + 1].play();
      this.$refs.video[this.videoIndex + 1].muted = false;
      this.videoIndex += val;
    },
    setClockFn(clockData) {
      this.isSetClock = !this.isSetClock;
      if (clockData) {
        if (this.clockIndex !== -1) {
          Object.assign(this.clockList[this.clockIndex], clockData);
        } else {
          this.clockList.push(Object.assign({}, this.clockItem, clockData));
        }
      }
      this.clockScene = clockData.message
    },
    confirm(list) {
      this.clockList = list;
      this.clockDrawer = false;
      this.showArrow = true;
    },
    setClockIndex(index) {
      this.isSetClock = true;
      this.clockIndex = index;
      if (index === -1) {
        this.clockItem = cloneDeep(clockList[0]);
      } else {
        this.clockItem = this.clockList[index];
        this.clockScene = this.clockItem.message
      }
      this.clockItem.videoIndex = this.videoIndex
    },
    beforeDrawerClose() {
      this.drawer = false;
      this.showArrow = false;
    },
    computeClock(timestamp) {
      if (!this.lastTimestamp) {
        this.lastTimestamp = timestamp;
      }
      const elapsed = timestamp - this.lastTimestamp;
      if (elapsed >= 1000) {
        const now = new Date();
        const hour = now.getHours();
        const min = now.getMinutes();
        this.nowHour = hour < 10 ? `0${hour}` : hour.toString();
        this.nowMin = min < 10 ? `0${min}` : min.toString();
        const isTimeOut = this.clockList.some((item) => {
          if (this.lastRing) {
            const { clock, minute } = this.lastRing;
            if (hour < Number(clock)) return false;
            if (hour === Number(clock) && min <= Number(minute)) return false;
          }
          if (
            hour === Number(item.clock) &&
            min === Number(item.minute) &&
            item.status
          ) {
            this.lastRing = item;
            this.$refs.video[this.videoIndex].muted = true
            this.clockScene = item.message
            this.videoIndex = item.videoIndex
            return true;
          }
        });
        if (isTimeOut) {
          const video = this.$refs.video[this.videoIndex];
          video.muted = false;
          this.drawer = false
          this.showArrow = false;
        }
      }
      window.requestAnimationFrame(this.computeClock);
    },
  },
};
</script>

<style>
#app {
  position: relative;
  width: 100vw;
  height: 100vh;
}
.el-icon-setting {
  position: absolute;
  top: 27px;
  left: 24px;
  font-size: 50px;
  color: #fff;
  opacity: 0.8;
  z-index: 1001;
}
.el-icon-arrow-up {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  font-size: 114px;
  width: 114px;
  margin: auto;
  color: #fff;
  opacity: 0.8;
  z-index: 10000;
}
.el-icon-arrow-down {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  font-size: 114px;
  width: 114px;
  margin: auto;
  color: #fff;
  opacity: 0.8;
  z-index: 10000;
}
.bg-videos {
  width: 100vw;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  overflow: hidden;
}
.video,
video {
  width: 100vw;
  height: 100vh;
  object-fit: fill;
}
.setting-drawer {
  background: rgba(0, 0, 0, 0.5);
  width: 144px !important;
}
.clock-scene {
  writing-mode: vertical-rl;
  color: #fff;
  font-size: 40px;
  margin: auto;
  letter-spacing: 10px;
  margin-top: 46px;
}
.small-scene {
  font-size: 24px;
  letter-spacing: 6px;
  margin-top: 10px;
}
.el-icon-plus {
  position: absolute;
  bottom: 20px;
  font-size: 30px;
  color: #fff;
  right: 0;
  left: 0;
  width: 20px;
  margin: auto;
  font-weight: bolder;
  z-index: 10001;
}
.clock-drawer {
  background: #353535;
  width: calc(100vw - 144px) !important;
}
.buttons {
  position: absolute;
  bottom: 30px;
  right: 30px;
  z-index: 10001;
}
.left-button {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 114px;
}
.options-btn {
  width: 100px;
  height: 44px;
  display: block;
  background: none;
  border: 3px solid #fff;
  font-weight: bolder;
  font-size: 25px;
  color: #d9d9d9;
  line-height: 0;
  margin-right: 0;
  margin-top: 10px;
}
.time-clock {
  font-size: 140px;
  color: #fff;
  position: absolute;
  right: 0;
  left: 0;
  top: 0;
  bottom: 0;
  margin: auto;
  width: 200px;
  height: 100px;
  z-index: 2;
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0.7;
}
</style>
