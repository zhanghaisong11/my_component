<template>
  <div class="img-box" @mousewheel="scroll" @touchmove.prevent v-show="isShowMode">
    <div class="close-button" @click="closeModel()">X</div>
    <img draggable="false" @mousedown="move" :src="img" :style="{width:imgWidth + 'px'}">
    <div class="left-button page-button" @click="before('last')">  </div>
    <div class="right-button page-button" @click="next('next')">  </div>
  </div>
</template>

<script>
  export default {
    name: "ImageViewer",
    data() {
      return {
        imgWidth: 400,
        isShowMode:false
      }
    },

    props: {
      config: {
        type: Object,
        default: function () {
          return {
            button: false
          }
        }
      },
      img: {
        type: String,
        default: function () {
          return ''
        }
      },
      closeCallback:{
        type: Function,
        default: function () {

        }
      },
      next: {
        type: Function,
        default: function () {

        }
      },
      before: {
        type: Function,
        default: function () {

        }
      }
    },
    mounted(){
      if(this.img && this.img!== ''){
        this.isShowMode = true
      }
      document.documentElement.style.overflow = "hidden";
    },
    watch:{
      img:function () {
        this.isShowMode = true
      }
    },
    methods: {
      closeModel:function(){
        this.isShowMode = false;
        this.closeCallback()
        document.documentElement.style.overflow = "auto";
      },
      scroll: function (e) {
        document.documentElement.style.overflow = "hidden";
        this.imgWidth = this.imgWidth + e.deltaY * 10
      },
      move: function (e) {
        let odiv = e.target;        //获取目标元素
        let disX = e.clientX - odiv.offsetLeft;   //算出鼠标相对元素的位置
        let disY = e.clientY - odiv.offsetTop;
        document.onmousemove = function(e){       //鼠标按下并移动的事件
          let left = e.clientX - disX;
          let top = e.clientY - disY;
          odiv.style.left = left + 'px';
          odiv.style.top = top + 'px';
        };
        document.onmouseup = function(e){         //解除时间绑定
          document.onmousemove = null;
          document.onmouseup = null;
        };
      }
    }
  }
</script>

<style scoped>
  .img-box {
    position: fixed;
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;
    height: 100vh;
    width: 100vw;
    background: rgba(0, 0, 0, 0.6);
    z-index: 3000;
    overflow: hidden;
    text-align: center;
    display: table-cell;
    vertical-align: middle;
  }

  .img-box img{
    position: absolute;
    left: calc((100vw - 400px)/2);
    top: 100px;
  }

  .img-box .page-button{
    position: absolute;
    width: 40px;
    height: 40px;
    text-align: center;
    display: table-cell;
    vertical-align: middle;
    border: white solid 1px;
    top: calc(50vh - 40px);
    border-radius: 40px;
    cursor: pointer;
  }

  .img-box .left-button{
    position: absolute;
    left: 20px;
  }

  .img-box .left-button:before{
    display: inline-block;
    content: " ";
    height: 18px;
    width: 18px;
    border-width: 4px 4px 0 0;
    border-color: #c7c7cc;
    border-style: solid;
    transform: rotate(225deg);
    position: absolute;
    top: 50%;
    right: 6px;
    margin-top: -9px;
  }

  .img-box .right-button{
    position: absolute;
    right: 20px;
    border-radius: 100px;
  }

  .img-box .right-button:before{
    display: inline-block;
    content: " ";
    height: 18px;
    width: 18px;
    border-width: 4px 4px 0 0;
    border-color: #c7c7cc;
    border-style: solid;
    transform: rotate(45deg);
    position: absolute;
    top: 50%;
    right: 12px;
    margin-top: -9px;
  }

  .img-box .close-button{
    position: absolute;
    right: 0;
    top: 0;
    background: white;
    border-radius: 0 0 0 40px;
    height: 50px;
    line-height: 50px;
    width: 50px;
    padding-left: 8px;
    font-size: 18px;
    color: white;
    background: rgba(0, 0, 0, 0.8);
  }

</style>
