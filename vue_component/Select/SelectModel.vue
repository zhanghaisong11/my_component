<template>
  <div class="select-box" v-cloak :style="domWidth">
    <div @click="taskSelectBoxClick" >
      <p class="fl" v-text="selectLabel"></p>
      <i class="fr el-icon-arrow-down"></i>
    </div>
    <ul class="option-box" v-show="isShowTaskSelect" :style="domWidth">
      <li v-for="(option,key) in options" :key="key" @click="chooseTaskType(option)" v-text="label === '' ? option : option[label]"></li>
    </ul>
  </div>
</template>

<script>
  export default {
    name: "SelectModel",
    data() {
      return {
        selectLabel: '',
        isShowTaskSelect: false,
        selectValue: '',
        domWidth:{width:'100px'}
      }
    },
    model: {
      prop: 'content',
      event: 'contentChange'
    },
    props: {
      content: {
        default: function () {
          return ''
        }
      },
      options: {
        type: Array,
        default: function () {
          return []
        }
      },
      width: {
        type: Number,
        default: function () {
          return 100
        }
      },
      valueKey: {
        type: String,
        default: function () {
          return ''
        }
      },
      valueKey: {
        type: String,
        default: function () {
          return ''
        }
      }
    },
    created(){
      this.domWidth.width = this.width + 'px'

    },
    mounted(){
    },
    methods: {
      taskSelectBoxClick: function () {
        if (this.isShowTaskSelect) {
          this.isShowTaskSelect = false;
        } else {
          this.isShowTaskSelect = true
        }
      },
      chooseTaskType: function (option) {
        if (this.label === '') {
          this.selectLabel = option;
          this.selectValue = option;
        } else {
          this.selectLabel = option[this.label];
          this.selectValue = option[this.value];
        }
        this.$emit('contentChange', this.selectValue);
        this.$emit('change', this.selectValue);
        this.isShowTaskSelect = false;
      }
    }

  }
</script>

<style scoped>

  [v-cloak] {
    display: none;
  }

  .select-box {
    height: 28px;
    line-height: 28px;
    border: solid #e3e3e3 1px;
    background: #f9f9f9;
    border-radius: 3px;
    padding-left: 20px;
    margin: 10px;
    cursor: pointer;
    position: relative;
  }

  .select-box div {
    height: 100%;
    width: 100%;
  }

  .select-box .option-box {
    position: absolute;
    width: 160px;
    text-align: center;
    top: 28px;
    padding: 0;
    background: #f9f9f9;
    left: 0px;
    max-height: 200px;
    overflow: auto;
  }

  .select-box .option-box li {
    font-size: 14px;
    color: #666666;
  }

  .select-box .option-box li:hover {
    background: #f0f0f0;

  }

  .select-box p {
    font-size: 14px;
    color: #666666;
  }

  .select-box i {
    margin: 5px;
    color: #666666;
  }


</style>
