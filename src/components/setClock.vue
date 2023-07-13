<template>
    <div class="clock">
        <div class="time">
            <input type="text" v-model="params.clock" @change="inpChange('clock')" @keyup="maxChange('clock')"
                maxlength="2">
            <span>:</span>
            <input type="text" v-model="params.minute" @change="inpChange('minute')" @keyup="maxChange('minute')"
                maxlength="2">
        </div>
        <div class="msg">
            <div class="date">
                <span>标签</span>
                <span>闹钟</span>
            </div>
            <div class="label">
                <span>日期</span>
                <span>每天</span>
            </div>
        </div>
        <button class="btn" @click="submit">确定</button>
    </div>
</template>

<script>
import { clockList } from './clock';
export default {
    data() {
        return {
            clockList,
            params: {
                minute: '',
                clock: '',
                message: ''
            }
        }
    },
    mounted() {
        this.params = Object.assign(this.params, this.clockList[this.$route.query.idx])
    },

    methods: {
        maxChange(type) {
            if (this.params[type] > 24 && type == 'clock') {
                this.params[type] = `24`
                return
            }

            if (this.params[type] > 60 && type == 'minute') {
                this.params[type] = `60`
                return
            }

        },

        inpChange(type) {
            if (this.params[type] == 0) {
                this.params[type] = `00`
                return
            }
            let num = this.params[type].replace(/^0+/, '')
            this.params[type] = this.params[type] < 10 ? `0${num}` : num
        },
        submit() {
            clockList[this.$route.query.idx] = Object.assign(clockList[this.$route.query.idx], this.params)
            this.$router.push({
                path: '/clock'
            })
        }
    }
}
</script>

<style scoped>
.clock {
    background: #353535;
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
    width: 80%;
}

/* .msg input {
    outline-style: none;
    height: 30px;
    font-size: 18px;
    width: 80%;
} */

.msg .date,.label {
    display: flex;
    justify-content: space-between;
    align-items: center;
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