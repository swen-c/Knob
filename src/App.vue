<template>
    <div class="app">
        <h1>Vue Knob Component</h1>
        <div class="knob-demo">
            <div class="knob-item">
                <h3>基本使用</h3>
                <Knob v-model="value1" />
                <p>當前值: {{ value1 }}</p>
            </div>

            <div class="knob-item">
                <h3>自訂範圍</h3>
                <Knob v-model="value2" :min="-50" :max="50" :start-angle="13" />
                <p>當前值: {{ value2 }}</p>
            </div>

            <div class="knob-item">
                <h3>自訂大小</h3>
                <Knob v-model="value3" :size="150" color="#000" />
                <p>當前值: {{ value3 }}</p>
            </div>

            <div class="knob-item">
                <h3>自訂顏色</h3>
                <Knob v-model="value4" color="#FF5722" />
                <p>當前值: {{ value4 }}</p>
            </div>

            <div class="knob-item">
                <h3>自訂起始角度</h3>
                <Knob v-model="value5" :start-angle="90" />
                <p>當前值: {{ value5 }}</p>
            </div>

            <div class="knob-item">
                <h3>自訂背景顏色</h3>
                <Knob v-model="value6" color="#f79743" backgroundColor="#ff7700" trackColor="#fad9c0"
                    :strokeWidth="100" />
                <p>當前值: {{ value6 }}</p>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue'
import Knob from './components/Knob.vue'

const value1 = ref(0)
const value2 = ref(0)
const value3 = ref(75)
const value4 = ref(25)
const value5 = ref(60)
const value6 = ref(50)
const value7 = ref(50)
</script>

<style>
.app {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
    text-align: center;
}

h1 {
    margin-bottom: 2rem;
}

.knob-demo {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 2rem;
}

.knob-item {
    padding: 1rem;
    border: 1px solid #eee;
    border-radius: 8px;
}

.knob-item h3 {
    margin-bottom: 1rem;
}
</style>