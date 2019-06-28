<template>
    <div>
        <div class="slider-item" data-type="0">
            <div class="content" :style="deleteSlider">
                <!-- 插槽中放具体项目中需要内容         -->
                <slot></slot>
            </div>
            <div class="remove" ref="remove" @click.prevent="deleteItem()">
                <span>删除</span>
            </div>
        </div>
    </div>
</template>
<script>
export default {
  data() {
    return {
      startX: 0, //触摸位置
      endX: 0, //结束位置
      moveX: 0, //滑动时的位置
      disX: 0, //移动距离
      deleteSlider: "", //滑动时的效果,使用v-bind:style="deleteSlider"
      lastIndex: "",
      startY: 0, //触摸位置
      endY: 0, //结束位置
      moveY: 0 //滑动时的位置
    };
  },
  props: {
    shoppingDelateStatus: {
      type: Number
    }
  },
  mounted() {
    let _this = this;
    let contents = document.getElementsByClassName("content");
    let removes = document.getElementsByClassName("remove");
    for (let i = 0; i < contents.length; i++) {
      contents[i].addEventListener("touchstart", function(ev) {
        for (let j = 0; j < contents.length; j += 1) {
          contents[j].style.webkitTransform = "translateX(0px)";
        }
        ev = ev || event;
        //tounches类数组，等于1时表示此时有只有一只手指在触摸屏幕
        //alert('ev.touches.length', ev.touches.length)
        if (ev.targetTouches.length == 1) {
          // alert('6666')
          // 记录开始位置
          _this.startX = ev.targetTouches[0].clientX;
          _this.startY = ev.targetTouches[0].clientY;
        }
      });
      contents[i].addEventListener("touchmove", function(ev) {
        //alert('0000')
        ev = ev || event;
        let parentElement = ev.currentTarget.parentElement;
        //获取删除按钮的宽度，此宽度为滑块左滑的最大距离
        let wd = removes[i].offsetWidth;
        if (ev.targetTouches.length == 1) {
          // 滑动时距离浏览器左侧实时距离
          _this.moveY = ev.targetTouches[0].clientY;
          _this.moveX = ev.targetTouches[0].clientX;
          if (Math.abs(_this.moveY - _this.startY) < 30) {
            //起始位置减去 实时的滑动的距离，得到手指实时偏移距离
            _this.disX = _this.startX - _this.moveX;
            // 如果是向右滑动或者不滑动，不改变滑块的位置
            if (_this.disX < wd / 2 || _this.disX == 0) {
              //_this.deleteSlider = "transform:translateX(0px)";
              contents[i].style.webkitTransform = "translateX(0px)";
              parentElement.dataset.type = 0;
              // 大于0，表示左滑了，此时滑块开始滑动
            } else if (_this.disX > wd / 2) {
              ev.preventDefault();
              //具体滑动距离我取的是 手指偏移距离*5。
              parentElement.dataset.type = 1;
              //_this.deleteSlider = "transform:translateX(-" + _this.disX + "px)";
              contents[i].style.webkitTransform =
                "translateX(-" + _this.disX + "px)";
              // 最大也只能等于删除按钮宽度
              if (_this.disX * 1.5 >= wd) {
                parentElement.dataset.type = 1;
                // _this.deleteSlider = "transform:translateX(-" + wd + "px)";
                contents[i].style.webkitTransform = "translateX(-" + wd + "px)";
              }
            }
          }
        }
      });
      contents[i].addEventListener("touchend", function(ev) {
        // alert('走了')
        ev = ev || event;
        let parentElement = ev.currentTarget.parentElement;
        let wd = removes[i].offsetWidth;
        if (ev.changedTouches.length == 1) {
          let endY = ev.changedTouches[0].clientY;
          if (Math.abs(_this.startY - endY) < 30) {
            let endX = ev.changedTouches[0].clientX;
            _this.disX = _this.startX - endX;
            //如果距离小于删除按钮一半,强行回到起点
            if (_this.disX < wd / 2) {
              parentElement.dataset.type = 0;
              //_this.deleteSlider = "transform:translateX(0px)";
              contents[i].style.webkitTransform = "translateX(0px)";
            } else {
              //大于一半 滑动到最大值
              parentElement.dataset.type = 1;
              //contents[i].deleteSlider = "transform:translateX(-" + wd + "px)";
              contents[i].style.webkitTransform = "translateX(-" + wd + "px)";
            }
          }
        }
      });
    }
  },
  methods: {
    deleteItem: function() {
      this.$emit("deleteItem", "0000");
      this.deleteSlider = "transform:translateX(0" + "rem)";
    }
  }
};
</script>
<style scoped lang="less">
.slider-item {
  width: 100%;
  position: relative;
  user-select: none;
  -webkit-overflow-scrolling: touch;
  .content {
    position: relative;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    z-index: 100;
    // 设置过渡动画
    transition: 0.3s;
  }
  .remove {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    position: absolute;
    width: 0.5rem;
    height: 97%;
    background: red;
    right: 0.01rem;
    top: 0.01rem;
    color: #fff;
    text-align: center;
    font-size: 0.14rem;
    img {
      width: 0.28rem;
      height: 0.28rem;
    }
    span {
      display: block;
      margin-top: 0.08rem;
      color: #fff;
      font-size: 0.14rem;
    }
  }
}
</style>
