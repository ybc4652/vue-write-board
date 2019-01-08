<template>
  <div class="home">
    <write-board></write-board>
  </div>
</template>

<script>
import writeBoard from './writeBoard'
  export default {
    name: 'home',
    components: { writeBoard },
    data() {
      return {
        
      }
    }
  }
</script>

<style lang="less" scoped>
  .home{
    height: 500px;
    overflow: auto;
  }
</style>