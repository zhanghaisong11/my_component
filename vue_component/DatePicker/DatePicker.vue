<template>
    <div class="modle-box" v-show="is_visible">
        <div class="date-pick-box">
            <div class="header line-height40">
                选择日期
                <img class="fr close pointer" src="./close-icon@2x.png" @click="calcle">
            </div>
            <div class="content">
                <div class="month-box text-center line-height40">
                    <img src="./left-image.png" class="fl pointer" @click="changeMonth(-1)">
                    <span v-text="showDate">2018年12月</span>
                    <img src="./right-image.png" class="fr pointer" @click="changeMonth(1)">
                </div>
                <ul class="week-list">
                    <li class="fl day-box text-center line-height40" v-for="(week, index) in weekList"
                        :key="'week' + index"
                        v-text="week"></li>
                </ul>
                <ul class="day-list">
                    <li class="day-box pointer fl text-center" @click="chooseDay(day, index)"
                        v-for="(day, index) in dayList" :key="'day' + index">
                        <div class="day" :class="{active: day.time === choosedDay}">
                            <p class="num font-14"
                               :class="{disable: day.isBeforeCurrentDay, 'color-blue': day.week === 0 || day.week === 6}"
                               v-text="day.day"></p>
                            <p class="text font-12 color-red" v-if="day.status === 0">休</p>
                            <p class="text font-12 color-red" v-if="day.status === 1">班</p>
                        </div>
                    </li>
                </ul>
                <div class="day-edit">
                    <p class="fl" v-text="chooseDate"></p>
                    <div class="fr button-box">
                        <button class="font-12 active" @click="setStatus(1)">上班</button>
                        <button class="font-12" @click="setStatus(0)">休息</button>
                    </div>
                </div>
                <p class="explain font-12">
                    仅可对明天及以后的日期进行工作日特殊调整。上方开关处于打开状态 即代表这一天工作
                </p>
            </div>
            <div class="bottom-box">
                <button class="confirm fr pointer" @click="confirm">确认</button>
                <button class="cancle fr pointer" @click="calcle">取消</button>
            </div>
        </div>
    </div>
</template>

<script>
  export default {
    name: 'DatePicker',
    props: {
      visible: {
        type: Boolean,
        default: !1
      },
      timeData: {
        type: Array,
        default() {
          return [];
        }
      }
    },
    data() {
      return {
        currentDay: '',
        is_visible: false,
        weekList: ['日', '一', '二', '三', '四', '五 ', '六'],
        dayList: [],
        currentYear: '2021',
        currentMonth: '1',
        choosedDay: '',
        choosedDayData: {},
        updateStatusDate: {}
      };
    },
    computed: {
      chooseDate() {
        let text = '';
        const {timeText, week} = this.choosedDayData;
        if (timeText) {
          text = timeText;
          text = text + ' 周' + this.weekList[week];
        }
        return text;
      },
      showDate() {
        return `${this.currentYear}年${this.rebuildNum(this.currentMonth)}月`;
      }
    },
    watch: {
      visible(bool) {
        if (bool) {
          this.is_visible = bool;
          this.initChooseData();
          this.getCurrentDate();
          this.buildDayList();
        } else {
          this.handleCancel();
        }
      }
    },
    created() {
      this.getCurrentDate();
    },
    methods: {
      chooseDay(dayData, index) {
        if (dayData.isBeforeCurrentDay) {
          return;
        }
        this.choosedDay = dayData.time;
        dayData.index = index;
        this.choosedDayData = dayData;
      },

      setStatus(status) {
        const {index} = this.choosedDayData;
        if (!index) {
          return this.bus.$emit('__emitError__', '请点击选择日期');
        }
        this.$set(this.dayList[index], 'status', status);
      },

      getCurrentDate() {
        const currentTime = new Date();
        this.currentYear = currentTime.getFullYear();
        this.currentMonth = currentTime.getMonth() + 1;
        this.currentDay = `${this.currentYear}-${this.currentMonth}-${currentTime.getDate()}`;
      },

      buildDayList() {
        this.dayList = [];
        const days = this.getDays(this.currentYear, this.currentMonth, 0);
        const week = this.getCurentWeek(this.currentYear, this.currentMonth);
        const lastMonthDays = this.getDays(this.currentYear, this.currentMonth, -1);
        for (let i = 0; i < week; i++) {
          this.dayList.unshift(this.buildDayData(lastMonthDays - i, -1, week));
        }
        for (let i = 1; i <= days; i++) {
          this.dayList.push(this.buildDayData(i, 0));
        }
        const nextMonthDays = 42 - this.dayList.length;
        for (let i = 1; i <= nextMonthDays; i++) {
          this.dayList.push(this.buildDayData(i, 1));
        }
      },

      buildDayData(day, num, week) {
        const {year, month} = this.rebuildMonth(this.currentYear, this.currentMonth, num);
        const rebuildMonth = this.rebuildNum(month);
        const rebuildDay = this.rebuildNum(day);
        let data = {
          day: day,
          time: `${year}-${rebuildMonth}-${rebuildDay}`,
          timeText: `${year}年${rebuildMonth}月${rebuildDay}日`,
          status: 3,
          isBeforeCurrentDay: this.compareTime(year, month, day)
        };
        if (this.updateStatusDate[data.time] || this.updateStatusDate[data.time] === 0) {
          data.status = this.updateStatusDate[data.time];
        }
        const length = this.dayList.length;
        if (week) {
          data.week = week - length - 1;
        } else {
          data.week = length % 7;
        }
        return data;
      },

      compareTime(year, month, day) {
        const [Y, M, D] = this.currentDay.split('-');
        const currentYear = Number(Y);
        const currentMonth = Number(M);
        const currentDay = Number(D);
        if (currentYear > year) {
          return true;
        } else if (currentYear === year && currentMonth > month) {
          return true;
        } else if (currentYear === year && currentMonth === month && currentDay > day) {
          return true;
        } else {
          return false;
        }
      },

      rebuildNum(num) {
        if (num > 9) {
          return num + '';
        } else {
          return '0' + num;
        }
      },

      // 获取当前月份总天数
      getDays(Y, M, num) {
        const add = num || 0;
        const {year, month} = this.rebuildMonth(Y, M, add);
        let date = new Date(year, month, 0);
        return date.getDate();
      },

      getCurentWeek(year, month) {
        return new Date(year + '/' + month + '/1').getDay();
      },

      dialogClosed() {
        this.$emit('update:visible', this.is_visible);
      },

      handleCancel() {
        this.is_visible = false;
        this.$emit('update:visible', false);
      },

      changeMonth(num) {
        this.buildChooseData();
        const {year, month} = this.rebuildMonth(this.currentYear, this.currentMonth, num);
        this.currentYear = year;
        this.currentMonth = month;
        this.buildDayList();
      },

      //只适用于月份加1减1
      rebuildMonth(year, month, num) {
        let newMonth = month + num;
        if (newMonth === 0) {
          newMonth = 12;
          year = year - 1;
        } else if (newMonth === 13) {
          newMonth = 1;
          year = year + 1;
        }
        return {
          year: year,
          month: newMonth
        };
      },

      initChooseData() {
        this.updateStatusDate = {};
        this.timeData.forEach((item) => {
          this.updateStatusDate[item.ateDate] = item.status;
        });
      },

      buildChooseData() {
        const list = this.dayList.filter((item) => {
          return item.status === 0 || item.status === 1;
        });
        list.forEach((item) => {
          this.updateStatusDate[item.time] = item.status;
        });
      },

      confirm() {
        this.buildChooseData();
        let sbumitData = [];
        Object.keys(this.updateStatusDate).forEach((item) => {
          sbumitData.push({
            ateDate: item,
            status: this.updateStatusDate[item]
          });
        });
        console.log(sbumitData);
        this.$emit('confirm', sbumitData);
      },

      calcle() {
        this.is_visible = false;
        this.$emit('update:visible', this.is_visible);
      }
    }
  };
</script>

<style scoped>
    .fl {
        float: left;
    }

    .fr {
        float: right;
    }

    .font-12 {
        font-size: 12px;
    }

    .font-14 {
        font-size: 14px;
    }

    .pointer {
        cursor: pointer;
    }

    .line-height40 {
        line-height: 40px;
    }

    .text-center {
        text-align: center;
    }

    .modle-box {
        z-index: 999;
        position: fixed;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        background: rgba(0, 0, 0, 0.43);
    }

    .date-pick-box {
        background: white;
        position: absolute;
        top: calc(50vh - 280px);
        left: 0;
        right: 0;
        margin: auto;
        width: 440px;
        height: 560px;
    }

    .date-pick-box .header {
        height: 40px;
        border-bottom: solid #E6E6E6 1px;
        padding: 0 19px;
    }

    .date-pick-box .header .close {
        height: 12px;
        width: 12px;
        margin: 14px 0;
    }

    .date-pick-box .content {
        padding: 0 32px;
    }

    .date-pick-box .month-box {
        width: 200px;
        margin: 20px auto 10px;
    }

    .date-pick-box .month-box img {
        height: 14px;
        width: 8px;
        margin: 13px 0;
    }

    .date-pick-box .week-list {
        height: 45px;
        border-bottom: solid 1px #ECEFF4;
    }

    .date-pick-box .day-box {
        width: 53px;
        height: 40px;
    }

    .date-pick-box .day-box .day {
        height: 40px;
        width: 40px;
        margin: auto;
        padding: 5px;
        box-sizing: border-box;
        border-radius: 40px;
        line-height: 14px;
        font-weight: bold;
    }

    .date-pick-box .day-box .day .disable {
        color: #C1C1C1;
    }

    .date-pick-box .day-box .day .num {
        line-height: 18px;
    }


    .date-pick-box .day-box .active {
        background: #2479ED;
    }

    .date-pick-box .day-box .active p {
        color: white !important;
    }

    .date-pick-box .day-list {
        height: 250px;
        padding-top: 10px;
    }

    .date-pick-box .day-edit {
        height: 25px;
        margin-top: 10px;
    }

    .date-pick-box .day-edit .button-box {
        width: 108px;
        height: 24px;
        background: #F4F9FF;
        border-radius: 8px;
    }

    .date-pick-box .day-edit .button-box button {
        width: 54px;
        height: 24px;
        border-radius: 8px;
        border: none;
        color: #333333;
    }

    .date-pick-box .day-edit .button-box .active {
        background: #2479ED;
        color: #ffffff;
    }

    .date-pick-box .explain {
        color: #999999;
        margin-top: 20px;
    }

    .bottom-box {
        margin-top: 19px;
        width: 100%;
        height: 50px;
        background: #FFFFFF;
        border-top: 1px solid #E6E6E6;
        position: relative;
        padding-top: 9px;
    }

    .bottom-box button {
        width: 78px;
        height: 32px;
        border-radius: 4px;
        margin-right: 10px;
    }

    .bottom-box .cancle {
        margin-right: 9px;
        background: #FFFFFF;
        border: 1px solid #E0E0E0;
        color: #666666;
    }

    .bottom-box .confirm {
        background: #3F9CFB;
        border: none;
        color: #ffffff;
    }

    .color-c1 {
        color: #C1C1C1;
    }

    .color-red {
        color: #FF3300;
    }

    .color-blue {
        color: #2479ED;
    }
</style>
