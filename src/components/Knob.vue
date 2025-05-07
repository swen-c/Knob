<template>
    <div class="knob-container" :style="{ width: size + 'px', height: size + 'px' }">
        <svg :width="size" :height="size" :viewBox="`0 0 ${size} ${size}`" class="knob-svg" ref="knobRef"
            @mousedown="handleMouseDown" @touchstart.prevent="handleTouchStart" @mousemove="handlePointerMove"
            @mouseleave="handlePointerLeave"
            :style="{ cursor: isDragging ? 'grabbing' : (isPointerOnRing ? 'pointer' : 'default') }">
            <!-- 進度條背景 -->
            <circle class="knob-track" :cx="size / 2" :cy="size / 2" :r="radius" :stroke="trackColor"
                :stroke-width="safeStrokeWidth" fill="none" />
            <!-- 進度條 -->
            <circle class="knob-progress" :cx="size / 2" :cy="size / 2" :r="radius" :stroke="color"
                :stroke-width="safeStrokeWidth" fill="none" :stroke-dasharray="circumference"
                :stroke-dashoffset="progressOffset"
                :style="{ transition: isDragging ? 'none' : 'stroke-dashoffset 0.2s', transform: `rotate(${startAngle - 90}deg)`, transformOrigin: '50% 50%' }"
                stroke-linecap="butt" />
            <!-- 中心背景 -->
            <g class="knob-center-content">
                <circle :cx="size / 2" :cy="size / 2" :r="Math.max(radius - safeStrokeWidth / 2, 0)"
                    :fill="backgroundColor" />
                <text x="50%" y="50%" text-anchor="middle" dominant-baseline="middle" class="knob-value">
                    {{ Math.round(value) }}
                </text>
            </g>
        </svg>
    </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const props = defineProps({
    modelValue: {
        type: Number,
        default: 0
    },
    min: {
        type: Number,
        default: 0
    },
    max: {
        type: Number,
        default: 100
    },
    size: {
        type: Number,
        default: 100
    },
    color: {
        type: String,
        default: '#4CAF50'
    },
    trackColor: {
        type: String,
        default: '#ddd'
    },
    startAngle: {
        type: Number,
        default: 0
    },
    backgroundColor: {
        type: String,
        default: '#fff'
    },
    strokeWidth: {
        type: Number,
        default: 8
    },
})

const emit = defineEmits(['update:modelValue', 'change'])

const knobRef = ref(null)
const value = ref(props.modelValue)
const isDragging = ref(false)
const isPointerOnRing = ref(false)

const safeStrokeWidth = computed(() => {
    const maxStroke = props.size / 2
    if (props.strokeWidth > maxStroke) {
        console.warn('[Knob] strokeWidth 不可大於 size/2，已自動限制為', maxStroke)
        return maxStroke
    }
    return props.strokeWidth
})

const stroke = safeStrokeWidth.value
const radius = computed(() => (props.size - stroke) / 2)
const circumference = computed(() => 2 * Math.PI * radius.value)

const minPercent = 0.02
const percentage = computed(() => {
    if (props.max === props.min) return 0
    let percent = (value.value - props.min) / (props.max - props.min)
    if (percent === 0) return minPercent
    return percent
})

const progressOffset = computed(() => circumference.value * (1 - percentage.value))

const getAngle = (x, y) => {
    const rect = knobRef.value.getBoundingClientRect()
    const centerX = rect.left + rect.width / 2
    const centerY = rect.top + rect.height / 2
    let angle = Math.atan2(y - centerY, x - centerX) * 180 / Math.PI
    // 讓0度在正上方
    angle = (angle + 90 + 360) % 360
    // 再加上 startAngle 的偏移
    angle = (angle - props.startAngle + 360) % 360
    return angle
}

const isOnRing = (x, y) => {
    const rect = knobRef.value.getBoundingClientRect()
    const centerX = rect.left + rect.width / 2
    const centerY = rect.top + rect.height / 2
    const dist = Math.sqrt((x - centerX) ** 2 + (y - centerY) ** 2)
    return dist >= radius.value - stroke / 2 && dist <= radius.value + stroke / 2
}

const handlePointerMove = (e) => {
    let x, y
    if (e.touches && e.touches[0]) {
        x = e.touches[0].clientX
        y = e.touches[0].clientY
    } else {
        x = e.clientX
        y = e.clientY
    }
    isPointerOnRing.value = isOnRing(x, y)
}

const handlePointerLeave = () => {
    isPointerOnRing.value = false
}

const updateValue = (newValue) => {
    const v = Math.round(Math.max(props.min, Math.min(props.max, newValue)))
    value.value = v
    emit('update:modelValue', v)
    emit('change', v)
}

const handleMouseDown = (e) => {
    let x = e.clientX
    let y = e.clientY
    if (!isOnRing(x, y)) return
    isDragging.value = true
    document.addEventListener('mousemove', handleMouseMove)
    document.addEventListener('mouseup', handleMouseUp)
    handleMouseMove(e)
}

const handleMouseMove = (e) => {
    if (!isDragging.value) return
    const angle = getAngle(e.clientX, e.clientY)
    const percent = angle / 360
    const newValue = props.min + (props.max - props.min) * percent
    updateValue(newValue)
}

const handleMouseUp = () => {
    isDragging.value = false
    document.removeEventListener('mousemove', handleMouseMove)
    document.removeEventListener('mouseup', handleMouseUp)
}

// 支援觸控
const handleTouchStart = (e) => {
    let x = e.touches[0].clientX
    let y = e.touches[0].clientY
    if (!isOnRing(x, y)) return
    isDragging.value = true
    document.addEventListener('touchmove', handleTouchMove, { passive: false })
    document.addEventListener('touchend', handleTouchEnd)
    handleTouchMove(e)
}

const handleTouchMove = (e) => {
    if (!isDragging.value) return
    const touch = e.touches[0]
    const angle = getAngle(touch.clientX, touch.clientY)
    const percent = angle / 360
    const newValue = props.min + (props.max - props.min) * percent
    updateValue(newValue)
}

const handleTouchEnd = () => {
    isDragging.value = false
    document.removeEventListener('touchmove', handleTouchMove)
    document.removeEventListener('touchend', handleTouchEnd)
}

watch(() => props.modelValue, (newValue) => {
    value.value = newValue
})
</script>

<style scoped>
.knob-container {
    display: inline-block;
}

.knob-svg {
    display: block;
}

.knob-track {
    transition: stroke 0.2s;
}

.knob-progress {
    transition: stroke-dashoffset 0.2s;
}

.knob-value {
    font-size: 1.2em;
    font-weight: bold;
    fill: #222;
    pointer-events: none;
}
</style>