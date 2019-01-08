<template>
  <div class="pen-props" :style="{'bottom': value ? '50px' : '-20px'}">
    <div class="pen-props-con">
      <div class="pen-props-main">
        <ul>
          <li v-for="(item, index) in props" :key="index" @click="showTool(item.value)">
            <Icons :type="item.icon"/>
            <p>{{item.label}}</p>
          </li>
        </ul>
      </div>
      <div class="pen-prop-tools" :style="{'bottom': showTools ? '-50px' : '-100px'}">
        <a class="pen-prop-cancel" @click="cancel"><Icons type="quxiao"></Icons></a>
        <ul v-if="prop === 'color'" class="pen-prop-colors">
          <li v-for="item in colors" :class="{'active': selected === item}" @click="select(item)">
            <span :style="{'background': item}"></span>
          </li>
        </ul>
        <ul v-if="prop === 'lineWidth'" class="pen-prop-sizes">
          <li v-for="item in sizes" :class="{'active': selected === item}" @click="select(item)">
            <span :style="{'width': item + 'px', 'height': item + 'px'}"></span>
          </li>
        </ul>
        <a class="pen-prop-confirm" @click="confirm"><Icons type="queding"></Icons></a>
      </div>
    </div>
  </div>
</template>

<script>
import Icons from './icons.vue'
  export default {
    name: 'penProps',
    components: { Icons },
    data() {
      return {
        props: [
          {
            value: 'color',
            icon: 'sepan',
            label: '颜色'
          },
          {
            value: 'lineWidth',
            icon: 'cuxi',
            label: '粗细'
          }
        ],
        param: {},
        showTools: false,
        prop: null,
        selected: null,
        colors: ['#000000', '#ffffff', '#2db7f5', '#ed4014', '#ff9900', '#19be6b'],
        sizes: [2, 4, 6, 8, 10]
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
    methods:{
      // 显示工具栏
      showTool (val) {
        // 设置属性
        if (!this.prop || this.prop !== val) {
          this.prop = val
        }
        // 设置选种值
        this.selected = this.param[this.prop]
        // 显示弹框
        if (!this.showTools) {
          this.showTools = true
        }
      },
      // 选择
      select (val) {
        this.selected = val
      },
      // 取消
      cancel () {
        this.showTools = false
      },
      // 确定
      confirm () {
        this.param[this.prop] = this.selected
        this.$emit('on-change', Object.assign({}, this.param))
        this.showTools = false
      }
    },
    watch: {
      value (val) {
        if(!val) this.showTools = false
      },
      data (val) {
        if(val) this.param = Object.assign({}, val)
      }
    },
    created () {
      this.param = Object.assign({}, this.data)
    }
  }
</script>

<style lang="less" scoped>
  .pen-props{
    position: absolute;
    width: 100%;
    height: 50px;
    left: 0;
    background: #fff;
    transition: all .3s linear;
    .pen-props-con{
      width: 100%;
      height: 100%;
      position: relative;
      .pen-props-main{
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        padding: 8px 0;
        height: 34px;
        background: #fff;
        z-index: 1;
        ul{
          display: inline-block;
          list-style: none;
          overflow: hidden;
          margin: 0 auto;
          padding: 0;
          li{
            color: #999;
            float: left;
            overflow: hidden;
            padding: 0 10px;
            margin-right: 10px;
            .iconfont{
              font-size: 18px;
            }
            p{
              margin: 0;
              font-size: 12px;
            }
          }
        }
      }
      .pen-prop-tools{
        position: absolute;
        left: 0;
        width: 100%;
        height: 100%;
        background: #f5f5f5;
        z-index: 10;
        transition: all .3s linear;
        .pen-prop-cancel,
        .pen-prop-confirm{
          float: left;
          overflow: hidden;
          margin: 10px;
          padding: 5px;
          color: #666;
          .iconfont{
            font-size: 18px;
          }
        }
        .pen-prop-confirm{
          float: right;
        }
        .pen-prop-colors,.pen-prop-sizes{
          list-style: none;
          overflow: hidden;
          margin: 0 auto;
          padding: 0;
          display: inline-block;
          li{
            float: left;
            width: 21px;
            height: 21px;
            text-align: center;
            line-height: 21px;
            margin: 15px 5px;
            border: 2px solid #f5f5f5;
            border-radius: 50%;
            font-size: 0;
            span{
              display: inline-block;
              border-radius: 50%;
              vertical-align: middle;
            }
            &.active{
              border-color: #515a6e;
            }
          }
        }
        .pen-prop-colors span{
          width: 15px;
          height: 15px;
        }
        .pen-prop-sizes span{
          background: #000;
        }
      }
    }
  }
</style>