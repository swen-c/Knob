<template>
    <div class="app">
        <h1>Knob 控制面板</h1>
        <div class="knob-control-panel">
            <div class="knob-preview">
                <Knob :key="`knob-${size}-${strokeWidth}-${startAngle}`" v-model="knobValue" :min="min" :max="max"
                    :size="size" :color="color" :track-color="trackColor" :start-angle="startAngle"
                    :background-color="backgroundColor" :stroke-width="strokeWidth" />
                <p class="current-value">當前值: {{ knobValue }}</p>
            </div>

            <div class="controls">
                <div class="control-group">
                    <h3>基本設定</h3>
                    <div class="control-item">
                        <label>數值範圍</label>
                        <div class="range-inputs">
                            <input type="number" v-model.number="min" placeholder="最小值" />
                            <span>至</span>
                            <input type="number" v-model.number="max" placeholder="最大值" />
                        </div>
                    </div>

                    <div class="control-item">
                        <label>大小 (px)</label>
                        <input type="range" v-model.number="size" min="50" max="200" step="1" />
                        <span>{{ size }}px</span>
                    </div>

                    <div class="control-item">
                        <label>起始角度</label>
                        <input type="range" v-model.number="startAngle" min="0" max="360" step="1" />
                        <span>{{ startAngle }}°</span>
                    </div>

                    <div class="control-item">
                        <label>線條寬度</label>
                        <input type="range" v-model.number="strokeWidth" min="1" max="100" step="1" />
                        <span>{{ strokeWidth }}px</span>
                    </div>
                </div>

                <div class="control-group">
                    <h3>顏色設定</h3>
                    <div class="control-item">
                        <label>進度條顏色</label>
                        <input type="color" v-model="color" />
                        <span>{{ color }}</span>
                    </div>

                    <div class="control-item">
                        <label>背景軌道顏色</label>
                        <input type="color" v-model="trackColor" />
                        <span>{{ trackColor }}</span>
                    </div>

                    <div class="control-item">
                        <label>中心背景顏色</label>
                        <input type="color" v-model="backgroundColor" />
                        <span>{{ backgroundColor }}</span>
                    </div>
                </div>

            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue'
import Knob from './components/Knob.vue'

// 基本參數
const knobValue = ref(50)
const min = ref(0)
const max = ref(100)
const size = ref(150)
const startAngle = ref(0)
const strokeWidth = ref(8)

// 顏色參數
const color = ref('#4CAF50')
const trackColor = ref('#ddd')
const backgroundColor = ref('#fff')

</script>

<style>
.app {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
}

h1 {
    text-align: center;
    margin-bottom: 2rem;
    color: #333;
}

.knob-control-panel {
    display: flex;
    gap: 2rem;
    padding: 2rem;
    background: #fff;
    border-radius: 12px;
    box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
}

.knob-preview {
    flex: 0 0 300px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1rem;
    padding: 1rem;
    background: #f8f8f8;
    border-radius: 8px;
}

.controls {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 2rem;
}

.control-group {
    background: #f5f5f5;
    padding: 1.5rem;
    border-radius: 8px;
}

.control-group h3 {
    margin: 0 0 1rem 0;
    color: #333;
}

.control-item {
    margin-bottom: 1rem;
    display: flex;
    align-items: center;
    gap: 1rem;
}

.control-item label {
    min-width: 120px;
    color: #666;
}

.control-item input[type="range"] {
    flex: 1;
}

.control-item input[type="color"] {
    width: 50px;
    height: 30px;
    padding: 0;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

.control-item span {
    min-width: 80px;
    color: #666;
}

.range-inputs {
    display: flex;
    align-items: center;
    gap: 0.5rem;
}

.range-inputs input {
    width: 80px;
    padding: 0.5rem;
    border: 1px solid #ddd;
    border-radius: 4px;
}

.preset-buttons {
    display: flex;
    gap: 1rem;
}

.preset-buttons button {
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 4px;
    background: #4CAF50;
    color: white;
    cursor: pointer;
    transition: background 0.3s;
}

.preset-buttons button:hover {
    background: #45a049;
}

.current-value {
    font-size: 1.2rem;
    color: #666;
    margin: 0;
    font-weight: bold;
}
</style>
