<template>
  <div class="demo-datetime-picker">
    <el-date-picker
        v-model="value1"
        type="datetime"
        placeholder="Select date and time"
    />
  </div>
</template>

<script>
import {onMounted, ref} from 'vue'
export default {
  name: 'ElTest',
  setup() {
    const value1 = ref(1)
    onMounted(() => {
      console.log('time', value1)
    })
    return {
      value1
    }
  }
}
</script>
<style scoped>
.demo-datetime-picker {
  display: flex;
  width: 100%;
  padding: 0;
  flex-wrap: wrap;
}
</style>
