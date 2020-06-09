<template>
    <transition name="message-fade">
        <div v-if="visible"  class="public-message text-center" :style="{'z-index':500 + seed}">
            <div class="message" >
                <p class="text" :class="type">{{message}}</p>
            </div>
        </div>
    </transition>
</template>

<script>
  export default {
    name: 'message',
    data() {
      return {
        visible: false,
        type: 'info',
        message: '',
        duration: 3000,
        seed: 1
      }
    },
    methods: {
      setTimer() {
        setTimeout(() => {
          this.close() // 3000ms之后调用关闭方法
        }, this.duration)
      },
      close() {
        this.visible = false
        setTimeout(() => {
          this.$destroy(true)
          this.$el.parentNode.removeChild(this.$el) // 从DOM里将这个组件移除
        }, 500)
      }
    },
    mounted() {
      this.setTimer() // 挂载的时候就开始计时，3000ms后消失
    }
  }
</script>
<style scoped>
    .public-message {
        position: absolute;
        height: 40px;
        display: table;
        line-height: 40px;
        top: 30px;
        width: 100%;
        text-align: center;
    }

    .message{
        display: table-cell;
        padding: 0 20px;
        border-radius: 5px;
        font-size: 14px;
    }

    .text{
        padding: 0 30px;
        border-radius: 5px;
        text-align: center;
        margin: 0px auto 0px;
        display: inline-block;
        font-size: 16px;
    }

    .success {
        background-color: #f0f9eb;
        color: #67c23a;
    }

    .info {
        background-color: #f4f4f5;
        color: #909399;
    }

    .warning {
        background-color: #fdf6ec;
        color: #e6a23c;
    }

    .error {
        background-color: #fef0f0;
        color: #f56c6c;
    }

</style>
