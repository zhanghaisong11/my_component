<template>
  <div class="select-box" v-cloak >
    <div @click="taskSelectBoxClick">
      <p class="fl" v-text="selectLabel"></p>
      <i class="fr el-icon-caret-bottom"></i>
    </div>
    <ul class="option-box" v-show="isShowTaskSelect">
      <li v-for="(option,key) in options" :key="key" @click="chooseTaskType(option)"
          v-text="labelKey === '' ? option : option[labelKey]"></li>
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
        selectValue: ''
      }
    },
    model: {
      prop:'content',
      event: 'contentChange'
    },
    props: {
      placeholder:{
        type: String,
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
      labelKey: {
        type: String,
        default: function () {
          return ''
        }
      },
      content:{
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
    created() {
      if(this.content === ''){
        this.selectLabel = this.placeholder
      }else{
        this.initSelectLabel();
      }
    },
    // watch:{
    //   content:function(value){
    //     this.initSelectLabel()
    //   }
    // },
    methods: {
      initSelectLabel:function(){
        if(this.valueKey === ''){
          this.selectLabel = this.content
        }else{
          this.getChooseOption();
        }
      },
      getChooseOption: function(){
        const that = this;
        this.options.forEach(function(option){
          if(that.content == option[that.valueKey]){
            that.selectLabel = option[that.labelKey]
          }
        })
      },

      taskSelectBoxClick: function () {
        if (this.isShowTaskSelect) {
          this.isShowTaskSelect = false;
        } else {
          this.isShowTaskSelect = true
        }
      },
      chooseTaskType: function (option) {
        if (this.labelKey === '') {
          this.selectLabel = option;
        } else {
          this.selectLabel = option[this.labelKey];
        }
        if (this.valueKey === '') {
          this.selectValue = option;
        } else {
          this.selectValue = option[this.valueKey];

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
    border: solid #e0f2ff 1px;
    background: #ffffff;
    border-radius: 2px;
    padding-left: 20px;
    cursor: pointer;
    position: relative;
    margin: 0;
  }

  .select-box div {
    height: 100%;
    width: 100%;
  }

  .select-box div p{
    width: calc(100% - 35px);
    text-align: center;
    overflow: hidden;
    text-overflow:ellipsis;
    white-space: nowrap;
    height: 30px;
  }

  .select-box .option-box {
    position: absolute;
    width: 100%;
    text-align: center;
    top: 28px;
    padding: 0;
    background: #f9f9f9;
    left: 0;
    z-index: 200;
    max-height: 200px;
    overflow: auto;

  }

  .select-box .option-box li {
    font-size: 14px;
    color: #666666;
    overflow: hidden;
    text-overflow:ellipsis;
    white-space: nowrap;
    width: 100%;
    height: 30px;
  }

  .select-box .option-box li:hover {
    background: #e0f2ff;

  }

  .select-box p {
    font-size: 14px;
    color: #666666;
  }

  .select-box i {
    margin: 5px;
    color: #666666;
  }

  .el-icon-caret-bottom{
    color: #38a8f0!important;
  }


</style>
