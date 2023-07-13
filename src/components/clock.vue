<template>
    <div class="clock">
        <ul>
            <li v-for="(item, idx) in clockList" :key="idx">
                <p class="time" @click="jumpSet(item, idx)">{{ item.clock }}: {{ item.minute }}</p>
                <p class="message" @click="jumpSet(item, idx)">{{ item.message }}</p>
                <el-switch v-model="item.status" active-color="#13ce66" inactive-color="#000000"></el-switch>
            </li>
        </ul>
    </div>
</template>

<script>
import { clockList } from './clock'
export default {
    data() {
        return {
            clockList
        }
    },
    methods: {
        jumpSet(item, idx) {
            this.$router.push({
                path: '/setClock',
                query: {
                    idx
                }
            })
        }
    }
}
</script>

<style scoped>
.clock {
    background: #353535;
    min-height: 100vh;
    padding: 16px 24px;
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
    width: 10%;
}
</style>