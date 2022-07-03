<template>
  <div class="demo-date-picker">
      <el-date-picker
          v-model="value1"
          type="date"
          placeholder="Pick a day"
      />
  </div>
</template>

<script>
import {onMounted, ref, watchEffect} from 'vue'
//import {useRoute} from "vue-router"
export default {
  name: 'ElTest',
  setup() {
    let value1 = ref('')
    onMounted(() => {
      console.log('time init', value1)
    })
    watchEffect(() => {
      console.log('time change', value1)
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
