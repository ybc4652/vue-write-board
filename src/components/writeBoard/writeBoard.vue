<template>
  <div class="write-board">
    <div ref="panel" class="write-board-panel">
      <div
        ref="eraser"
        class="write-board-eraser"
        v-if="eraserProps.show"
        :style="{'top': eraserProps.top + 'px', 'left': eraserProps.left + 'px', 'width': eraserProps.width + 'px', 'height': eraserProps.height + 'px'}"
      ></div>
      <canvas ref="canvas" :width="canvasProps.width" :height="canvasProps.height"></canvas>
    </div>
    <div class="write-board-footer">
      <div class="write-board-footer-content">
        <!-- 更多操作 -->
        <div class="write-board-more" :style="{'bottom': showMore ? '51px' : '0px'}">
          <ul class="write-board-tools">
            <li @click="adjust">
              <Icons type="tiaozheng" :size="18" />
              <p>调整画布</p>
            </li>
            <li @click="upload">
              <Icons type="tupian" :size="18" />
              <p>上传图片</p>
            </li>
            <li @click="reset">
              <Icons type="shuaxin" :size="18" />
              <p>重置画布</p>
            </li>
            <li @click="changeColor">
              <Icons type="sepan" :size="18" />
              <p>画笔颜色</p>
            </li>
            <li @click="history">
              <Icons type="lishi" :size="18" />
              <p>历史记录</p>
            </li>
          </ul>
        </div>
        <!-- 基础操作 -->
        <ul class="write-board-tools">
          <li :class="{'active': historyList.length > 0 && current !== 0}" @click="revoke">
            <Icons type="chexiao" :size="18" />
            <p>撤销</p>
          </li>
          <li :class="{'active': historyList.length > 0 && current !== historyList.length}" @click="recover">
            <Icons type="fanchexiao" :size="18" />
            <p>恢复</p>
          </li>
          <li :class="{'active': active === 1}" @click="pen">
            <Icons type="bi" :size="18" />
            <p>画笔</p>
          </li>
          <li :class="{'active': active === 2}" @click="rubber">
            <Icons type="xiangpica" :size="18" />
            <p>橡皮擦</p>
          </li>
          <li :class="{'active': active === 3}" @click="more">
            <Icons type="gengduo" :size="18" />
            <p>更多</p>
          </li>
        </ul>
        <!-- 画笔属性面板 -->
        <div class="write-board-pen-props">
          <ul>
            <li>
              <Icons type="yanse" :size="18" />
              <p>颜色</p>
            </li>
            <li>
              <Icons type="yanse" :size="18" />
              <p>颜色</p>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import Icons from '../icons'
export default {
  name: 'writeBoard',
  data () {
    return {
      // canvas属性
      canvasProps: {
        width: 0,
        height: 0,
      },
      // 获取焦点的工具 1笔 2橡皮擦 3更多
      active: 1,
      // 上一步焦点
      oldActive: 1,
      // 显示更多
      showMore: false,
      // 画笔属性
      showPenTools: false,
      color: '#000',
      lineWidth: 2,
      // 橡皮属性
      showEraser: false,
      eraserProps: {
        show: false,
        width: 10,
        height: 10,
        top: -100,
        left: -100
      },
      // canvas实例
      paint: null,
      // 起始位置
      startX: null,
      startY: null,
      // 历史相关
      historyList: [],
      current: 0
    }
  },
  components: {
    Icons
  },
  methods:{
    // 初始化
    init () {
      this.canvasInit()
      // 进行事件监听
      this.canvasEvent()
    },
    // 实例画布
    canvasInit () {
      // 设置canvas宽高
      this.canvasProps.width = this.$refs.panel.clientWidth
      this.canvasProps.height = this.$refs.panel.clientHeight
      // 实例化canvas
      this.paint = this.$refs.canvas.getContext("2d")
      //背景色
      this.paint.fillStyle = "#fff"
      this.paint.fillRect(0, 0, this.canvasProps.width, this.canvasProps.height)
    },
    /**
     * 工具方法
     */
    // 撤销
    revoke () {
      let _self = this
      if (this.current === 0) return
      this.current = this.current - 1
      // 重新初始化
      this.canvasInit()
      let img = new Image()
      img.src = this.historyList[this.current - 1]
      img.onload = function () {
        _self.paint.drawImage(img, 0, 0)
      }
    },
    // 恢复
    recover () {
      let _self = this
      if (this.current === this.historyList.length) return
      this.current = this.current + 1
      // 重新初始化
      this.canvasInit()
      let img = new Image()
      img.src = this.historyList[this.current - 1]
      img.onload = function () {
        _self.paint.drawImage(img, 0, 0)
      }
    },
    // 画笔
    pen () {
      if (this.active !== 1) {
        this.resetPen()
      } else {
        this.showPenTools = !this.showPenTools
      }
    },
    // 橡皮擦
    rubber () {
      this.active = 2
    },
    // 更多
    more () {
      this.active === 3 ? this.active = 1 : this.active = 3
      this.showMore = !this.showMore
    },
    // 调整画布
    adjust () {

    },
    // 上传图片
    upload () {

    },
    // 重置画布
    reset () {

    },
    // 画笔颜色
    changeColor () {

    },
    // 历史记录
    history () {

    },
    // 重置到笔的状态
    resetPen () {
      this.active = 1
      // 关闭最多
      this.showMore = false
    },
    // 保存历史
    stackImgs() {
      let image = this.$refs.canvas.toDataURL("image/png")
      this.historyList.push(image)
      this.current = this.historyList.length
    },
    // 监听
    canvasEvent () {
      let _self = this
      let endX
      let endY
      this.$refs.canvas.addEventListener('touchstart', (event) => {
        //记录触屏的第一个点
        _self.startX = event.touches[0].clientX
        _self.startY = event.touches[0].clientY
        // 判断当前图片位置
        if (_self.current !== _self.historyList.length) {
          _self.historyList.splice(_self.current, _self.historyList.length)
        }
        //如果处于橡皮檫的状态
        if (_self.active === 2) {
            //显示橡皮檫那div
            _self.eraserProps.show = true
            _self.eraserProps.top = _self.startY - _self.eraserProps.height * 0.5
            _self.eraserProps.left = _self.startX - _self.eraserProps.width * 0.5
        } else {
          _self.resetPen()
        }
      })
      // 移动
      this.$refs.canvas.addEventListener('touchmove', (event) => {
        event.preventDefault();
        endX = event.touches[0].clientX;
        endY = event.touches[0].clientY;
        if (_self.active === 1) {//绘图
            //画下线段
            _self.paint.beginPath();
            _self.paint.moveTo(_self.startX, _self.startY);
            _self.paint.lineTo(endX, endY);
            _self.paint.closePath();
            //动态的设置颜色
            _self.paint.strokeStyle = _self.color;
            _self.paint.lineWidth = _self.lineWidth;
            _self.paint.stroke();
        } else {
            //橡皮擦
            _self.eraserProps.top = _self.startY - _self.eraserProps.height * 0.5
            _self.eraserProps.left = _self.startX - _self.eraserProps.width * 0.5
            let left = _self.$refs.eraser.offsetLeft
            let top = _self.$refs.eraser.offsetTop
            _self.paint.clearRect(left, top, _self.eraserProps.width, _self.eraserProps.height);
        }
        _self.startX = endX;
        _self.startY = endY;
        
      })
      // 手指抬起操作
      this.$refs.canvas.addEventListener('touchend', (event) => {
        _self.stackImgs()
        _self.eraserProps.show = false
      })
    }
  },
  mounted(){
    this.init()
  }
}
</script>
<style lang="less" scoped>
@import url('./writeBoard.less');
</style>