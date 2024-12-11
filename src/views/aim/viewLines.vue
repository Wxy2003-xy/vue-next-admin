<template lang="">
<div style="position: absolute; align-items: center; width: 1200px; height: 600px; background-color: transparent">
    <svg height="100%" width="100%" xmlns="http://www.w3.org/2000/svg">
      <line
        :x1="x1"
        :y1="y1"
        :x2="x2"
        :y2="y2"
        :style="{ stroke: 'red', strokeWidth: 2, strokeOpacity: 0.5 }"
      />
    </svg>
    <button @click="updateLine">Change Line</button>
  </div>
</template>
<script lang="ts">
import { ref } from 'vue'
export default {
  props: {
    x1: { type: Number, default: 0 },
    y1: { type: Number, default: 0 },
    x2: { type: Number, default: 800 },
    y2: { type: Number, default: 600 },
    color: { type: String, default: 'red' },
    width: { type: Number, default: 800 },
    height: { type: Number, default: 600 }
  },
  setup(props) {
    const x1 = ref(props.x1);
    const y1 = ref(props.y1);
    const x2 = ref(props.x2);
    const y2 = ref(props.y2);

    const updateLine = () => {
      x1.value = 50;
      y1.value = 50;
      x2.value = 250;
      y2.value = 150;
    };

    return { x1, y1, x2, y2, updateLine };
  }
};
</script>
<style lang="">
    
</style>