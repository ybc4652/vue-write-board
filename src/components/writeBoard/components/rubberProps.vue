<template>
  <div class="rubber-props" :style="{'bottom': show ? '50px' : '-10px'}">
    <a class="rubber-prop-cancel" @click="cancel"><Icons type="quxiao"></Icons></a>
    <ul class="rubber-prop-sizes">
      <li v-for="item in sizes" :class="{'active': param.size === item}" @click="select(item)">
        <span :style="{'width': item + 'px', 'height': item + 'px'}"></span>
      </li>
    </ul>
    <a class="rubber-prop-confirm" @click="confirm"><Icons type="queding"></Icons></a>
  </div>
</template>

<script>
import Icons from './icons.vue'
export default {
  name: 'rubberProps',
  components: { Icons },
  data() {
    return {
      sizes: [5, 10, 15, 20],
      param: {}
    }
  },
  props:{
    value: {
      type: Boolean,
      default: false
    },
    data:{
      type: Object,
      default () {
        return {}
      }
    }
  },
  computed: {
    show:{
      get () {
        return this.value
      },
      set (val) {
        this.$emit('input', val)
      }
    }
  },
  methods:{
    // 选择
    select (val) {
      this.param.size = val
    },
    // 取消
    cancel () {
      this.show = false
    },
    // 确定
    confirm () {
      this.$emit('on-change', Object.assign({}, this.param))
      this.show = false
    }
  },
  watch: {
    data (val) {
      if (val) this.param = Object.assign({}, val)
    }
  },
  created () {
    this.param = Object.assign({}, this.data)
  }
}
</script>

<style lang="less" scoped>
  .rubber-props{
    position: absolute;
    width: 100%;
    height: 50px;
    left: 0;
    background: #f5f5f5;
    box-shadow: 0 0 10px #eee;
    border-bottom: 1px solid #eee;
    transition: all .3s linear;
    .rubber-prop-cancel,
    .rubber-prop-confirm{
      float: left;
      overflow: hidden;
      margin: 10px;
      padding: 5px;
      color: #666;
      .iconfont{
        font-size: 18px;
      }
    }
    .rubber-prop-confirm{
      float: right;
    }
    .rubber-prop-sizes{
      list-style: none;
      overflow: hidden;
      margin: 0 auto;
      padding: 0;
      display: inline-block;
      li{
        float: left;
        margin:14px 5px;
        width: 22px;
        height: 22px;
        line-height: 22px;
        border: 2px solid #f5f5f5;
        border-radius: 50%;
        &.active{
          border-color: #999;
        }
        span{
          display: inline-block;
          background: #fff;
          vertical-align: middle;
          border-radius: 50%;
        }
      }
    }
  }
</style>