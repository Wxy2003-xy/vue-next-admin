<template>
    <div class="canvas"@mousemove="mouseMove" @click="shoot">
        <div class="alert-penal" id="banner">
            <div style="max-width: 600px">
                <el-alert title="Success alert" type="success" />
            </div>
        </div>
        <div class="guidelines">
            <viewLine
            :x1="x1"
            :y1="y1"
            :x2="x2"
            :y2="y2"
            :color="'blue'"
            :width="w"
            />
            <viewLine
            :x1="x11"
            :y1="y11"
            :x2="x22"
            :y2="y22"
            :color="'blue'"
            :width="w2"
            />
        </div>
        <div class="select-counter">
            <el-input-number v-model="num" :min="1" :max="10" @change="handleChange" />
        </div>
        <button 
        class="start-button" 
        @click="start"
        :style="{
            backgroundColor: 'blue',
            color: 'white',
            padding: '10px 20px',
            border: 'none',
            borderRadius: '5px',
            cursor: 'pointer'}">
                Start
        </button>
        <button 
            @click="shootOnTarget"
            class="target"
            :style="style">
            <p>b</p>
        </button>
    </div>
    <div class="stats-board">
        <el-row>
            <el-col :span="6">
            <el-statistic title="Daily active users" :value="mouseCoords.x" />
            </el-col>
            <el-col :span="6">
            <el-statistic title="Total Transactions" :value="outputValue" />
            </el-col>
            <el-col :span="6">
            <el-statistic title="Total count" :value="count">
                <template #suffix>
                <el-icon style="vertical-align: 0.125em">
                    <ChatLineRound />
                </el-icon>
                </template>
            </el-statistic>
            </el-col>
            <el-col :span="6">
            <el-statistic title="Total Shot" :value="totalClick">
                <template #suffix>
                <el-icon style="vertical-align: 0.125em">
                    <ChatLineRound />
                </el-icon>
                </template>
            </el-statistic>
            </el-col>
            <el-col :span="16">
            <el-statistic title="Accuracy" :value="accuracy">
                <template #suffix>
                <el-icon style="vertical-align: 0.125em">
                    <ChatLineRound />
                </el-icon>
                </template>
            </el-statistic>
            </el-col>
        </el-row>
    </div>
    <div class="chart">
        <CanvasJSChart :options="options" :style="styleOptions" @chart-ref="chartInstance"/>
    </div>
</template>

<script setup lang="ts" name="aim">
import { watch, ref, computed } from 'vue'
import viewLine from './viewLines.vue'
import { useTransition } from '@vueuse/core'
import { ChatLineRound } from '@element-plus/icons-vue'
import CanvasJSChart from '@canvasjs/vue-charts';
var started = ref(false);
var count = ref(0);
var totalClick = ref(0);
var accuracy = ref(100);
// initialised to (0,0)
const x1 = ref(0);
const y1 = ref(0);
const x2 = ref(screen.width * 0.8);
const y2 = ref(screen.height * 0.5);
const w = ref(10);
const x11 = ref(0);
const y11 = ref(screen.height * 0.5);
const x22 = ref(screen.width * 0.8);
const y22 = ref(0);
const w2 = ref(10);
const mouseCoords = ref({x: 0, y: 0})
const variableSize = ref(20)
const halfVariableSize = ref(10)
const chart = ref(null);
const options = ref({
  theme: "light2", // "light1", "dark1", "dark2"
  animationEnabled: true, // change to true
  animationDuration: 3000,
  title: {
    text: "BTC/USD"
  },
  axisY: {
    title: "Closing Price in USD",
    prefix: "$"
  },
  data: [{
    type: "line", // Change type to "bar", "area", "spline", "pie",etc.
    xValueFormatString: "MMM DD, YYYY",
    yValueFormatString: "$#,###.00",
    markerSize: 0,
    dataPoints: [
      { x: new Date("2020-01-01"), y: 8163.692383 },
      { x: new Date("2020-01-08"), y: 8827.764648 },
      { x: new Date("2020-01-15"), y: 8745.894531 },
      { x: new Date("2020-01-22"), y: 9358.589844 },
      { x: new Date("2020-01-29"), y: 9180.962891 },
      { x: new Date("2020-02-05"), y: 10208.23633 },
      { x: new Date("2020-02-12"), y: 10141.99609 },
      { x: new Date("2020-02-19"), y: 9341.705078 },
      { x: new Date("2020-02-26"), y: 8787.786133 },
      { x: new Date("2020-03-04"), y: 7909.729492 },
      { x: new Date("2020-03-11"), y: 5225.629395 },
      { x: new Date("2020-03-18"), y: 6734.803711 },
      { x: new Date("2020-03-25"), y: 6438.644531 },
      { x: new Date("2020-04-01"), y: 7176.414551 },
      { x: new Date("2020-04-08"), y: 6842.427734 },
      { x: new Date("2020-04-15"), y: 6880.323242 },
      { x: new Date("2020-04-22"), y: 7807.058594 },
      { x: new Date("2020-04-29"), y: 9003.070313 },
      { x: new Date("2020-05-06"), y: 8804.477539 },
      { x: new Date("2020-05-13"), y: 9729.038086 },
      { x: new Date("2020-05-20"), y: 8835.052734 },
      { x: new Date("2020-05-27"), y: 9529.803711 },
      { x: new Date("2020-06-03"), y: 9795.700195 },
      { x: new Date("2020-06-10"), y: 9538.024414 },
      { x: new Date("2020-06-17"), y: 9629.658203 },
      { x: new Date("2020-06-24"), y: 9137.993164 },
      { x: new Date("2020-07-01"), y: 9252.277344 },
      { x: new Date("2020-07-08"), y: 9243.213867 },
      { x: new Date("2020-07-15"), y: 9374.887695 },
      { x: new Date("2020-07-22"), y: 10912.82324 },
      { x: new Date("2020-07-29"), y: 11205.89258 },
      { x: new Date("2020-08-05"), y: 11410.52539 },
      { x: new Date("2020-08-12"), y: 11991.2334 },
      { x: new Date("2020-08-19"), y: 11366.13477 },
      { x: new Date("2020-08-26"), y: 11970.47852 },
      { x: new Date("2020-09-02"), y: 10131.5166 },
      { x: new Date("2020-09-09"), y: 10796.95117 },
      { x: new Date("2020-09-16"), y: 10538.45996 },
      { x: new Date("2020-09-23"), y: 10844.64063 },
      { x: new Date("2020-09-30"), y: 10604.40625 },
      { x: new Date("2020-10-07"), y: 11425.89941 },
      { x: new Date("2020-10-14"), y: 11916.33496 },
      { x: new Date("2020-10-21"), y: 13654.21875 },
      { x: new Date("2020-10-28"), y: 13950.30078 },
      { x: new Date("2020-11-04"), y: 15290.90234 },
      { x: new Date("2020-11-11"), y: 17645.40625 },
      { x: new Date("2020-11-18"), y: 19107.46484 },
      { x: new Date("2020-11-25"), y: 18802.99805 },
      { x: new Date("2020-12-02"), y: 18321.14453 },
      { x: new Date("2020-12-09"), y: 19417.07617 },
      { x: new Date("2020-12-16"), y: 23783.0293 },
      { x: new Date("2020-12-23"), y: 27362.4375 },
      { x: new Date("2020-12-30"), y: 29001.7207 }
    ]
  }]
});
const styleOptions = ref({
  width: "100%",
  height: "360px"
});

const chartInstance = (chartRef) => {
  chart.value = chartRef;
};
const start = async () => {
    toggleBannerVisibility();
    started.value = true; // Update the reactive variable
    await delay(10);
    totalClick.value = 0;
};

const toggleBannerVisibility = async () => {
    var banner = document.getElementById("banner");
    console.log('found')
    if (banner?.style.display == "none") {
            console.log('display')
            banner.style.display = "block";
        await delay(1000)
        banner.style.display = "none";
    }
}
const targetCoords = ref({
    x: 825,
    y: 825,
})

const mouseMove = (event: MouseEvent): void => {
    mouseCoords.value.x = event.clientX;
    mouseCoords.value.y = event.clientY;
    source.value = mouseCoords.value.x

}

const style = computed(() => ({
    position: 'absolute',
    left: targetCoords.value.x + '%',
    top: targetCoords.value.y + '%',
    width: variableSize.value + 'px',
    height: variableSize.value + 'px',
    borderRadius: variableSize.value + 'px',
    backgroundColor: 'red'
}));

const generateNewTarget = (): void => {
    targetCoords.value.x = Math.floor(Math.random() * 100)
    targetCoords.value.y = Math.floor(Math.random() * 100)
    variableSize.value = Math.floor(Math.random() * 50) + 20
    halfVariableSize.value = Math.floor(variableSize.value / 2);
}
const delay = (ms: number) => new Promise(resolve => setTimeout(resolve, ms));
const session = async () => {
    for (let i = 0; i < num.value; i++) {
        await delay(1000);
        generateNewTarget();
        totalClick.value++;
    }
    await delay(1000);
    started.value = false
    var banner = document.getElementById("banner");
    banner.style.display = "none";

}

const num = ref(1)
const handleChange = (value: number) => {
  console.log(value)
}

const shootOnTarget = () => {
    if (started.value) {
        count.value++;
        totalClick.value--;
        computeAccuracy();
    }
}
const shoot = () => {
    if (started.value) {
        totalClick.value++;
        computeAccuracy();
    }
}
const computeAccuracy = () => {
    if (totalClick.value > 0) {
        accuracy.value = count.value * 10000 / totalClick.value / 100
    } else {
        accuracy.value = 100;

    }
}
const source = ref(0)
const outputValue = useTransition(source, {
  duration: 1,
})

watch(
  started,
  (newValue) => {
    if (newValue) {
      count.value = 0;
      totalClick.value = 0;
      session();
    }
  }
);
</script>

<style scoped lang="scss">
.target {
    /* Optional: additional styling for the target button */
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
}
.canvas {
    display: block;
    flex-direction: column;
}
.guidelines {
    display: block;
    background-color: transparent;
}
.start-button {
    margin: 20px;
    position: absolute;
}
.stats-board {
    position: absolute;
    bottom: 5%;
    align-items: center;
    align-self: start;
    margin-left: 5%;
}
.select-counter {
    position: absolute;
    bottom: 5%;
    left: 50%;
    align-self: center;
    align-items: center;
    background-color: transparent;
}

.alert-penal {
    align-content: center;
    position: absolute; 
    left: 45%;
    right: 45%;
    top: 5%;
    z-index: 1000;
}

.el-alert {
    justify-self: center;
    min-width: 200px;
    margin: 20px 0 0;
}
.el-alert:first-child {
  margin: 0;
}
.chart {
    background-color: antiquewhite;
}
</style>
