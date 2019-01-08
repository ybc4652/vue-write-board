<template>
  <i :class="`iconfont icon-${type}`" :style="styles"></i>
</template>

<script>
export default {
  name: 'Icons',
  props: {
    type: {
      type: String,
      required: true
    },
    size: {
      type: Number,
      default: null
    }
  },
  computed: {
    styles () {
      let obj = {}
      if (this.size) {
        obj.fontSize = `${this.size}px`
      }
      return obj
    }
  }
}
</script>

<style lang="less">
@import url('../assets/icons/iconfont.css');
</style>
