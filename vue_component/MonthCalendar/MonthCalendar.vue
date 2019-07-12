<template>
  <section class="month-calendar">
    <div>
      <ul class="day-ul" style="height: 30px;">
        <li class="fl day-box text-center">日</li>
        <li class="fl day-box text-center">一</li>
        <li class="fl day-box text-center">二</li>
        <li class="fl day-box text-center">三</li>
        <li class="fl day-box text-center">四</li>
        <li class="fl day-box text-center">五</li>
        <li class="fl day-box text-center">六</li>
      </ul>
      <ul class="day-ul">
        <li v-for="(day,key) in monthNumArr" :key="key" class="fl day-box text-center relative"
            :style="getBackColor(pollutionType, externalData[day < 10 ? day : day])"
            :class="type == 1 ? 'font1' :'font2'">
          <p v-text="day !== 0 ? day : ''" :style="{'margin-top': type == 1? '3px': 0 }"></p>
          <p v-if="type == 1" style="font-size: 12px;top: 15px;line-height: 12px;margin-top: 3px"
             v-text="externalData[day < 10 ? day : day] ?  externalData[day < 10 ? day : day].mainPol? externalData[day < 10 ? day : day].mainPol : '' : ''"></p>
        </li>
      </ul>
    </div>

  </section>
</template>

<script>
  export default {
    name: "month-calendar",
    data() {
      return {
        monthNumArr: []
      }
    },
    props: {
      type: {                                    //组件类型
        type: Number,
        default: function (data) {
          return 0
        }
      },
      year: {
        type: Number
      },
      month: {
        type: Number
      },
      externalData: {                               //月数据
        type: Object,
        default: function (data) {
          return {}
        }
      },
      pollutionType: {                             //污染物类型
        type: String
      }
    },
    created() {
      this.monthNumArr = this.rebuildDaysArr();
    },
    methods: {
      getMainPollution: function (dayData,type) {
        if(dayData){


        }else{
          return ''
        }
      },
      mGetDate: function (year, month) {
        const d = new Date(year, month, 0);
        return d.getDate();
      },
      rebuildDaysArr: function () {
        let monthDays = []
        const daysNum = this.mGetDate(this.year, this.month)
        const currentWeek = new Date(this.year + '/' + this.month + '/1').getDay()
        for (let i = 0; i < currentWeek; i++) {
          monthDays.push(0)
        }
        for (let i = 1; i <= daysNum; i++) {
          monthDays.push(i)
        }
        for (let i = monthDays.length; i < 42; i++) {
          monthDays.push(0)
        }
        return monthDays
      },
      checkLevel: function (type, num) {             //获取污染等级
        if (Number(num) == -1 || num == 0) return 7
        const standardData = {
          "aqi": [0, 50, 100, 150, 200, 300],
          "so2": [0, 150, 500, 650, 800, 1600],
          "no2": [0, 100, 200, 700, 1200, 2340],
          "pm10": [0, 50, 150, 250, 350, 420],
          "co": [0, 5, 10, 35, 60, 90],
          "o3": [0, 160, 200, 300, 400, 800],
          "pm25": [0, 35, 75, 115, 150, 250],
          "zonghezhishu": [0, 5.9, 6.9, 7.9, 8.9, 9],
        }
        let level = 1;
        if (standardData[type]) {
          standardData[type].forEach(function (value, key) {
            if (Number(num) > value) {
              level = key + 1
            }
          })
        }
        return level
      },
      getBackColor: function (type, data) {
        if (!data) return {background: '#ffffff', color: '#000000'}
        let num = data[type]
        let pollutionType = type
        if (this.type == 1) {
          num = data['aqi']
          pollutionType = 'aqi'
        }
        if (!num) return {background: '#ffffff', color: '#000000'}
        const currentLevel = this.checkLevel(pollutionType, num)
        if (currentLevel == 6) {
          return {background: '#7d0021', color: '#ffffff'}
        } else if (currentLevel == 5) {
          return {background: '#97004c', color: '#ffffff'}
        } else if (currentLevel == 4) {
          return {background: '#f90000', color: '#ffffff'}
        } else if (currentLevel == 3) {
          return {background: '#fa7f00', color: '#ffffff'}
        } else if (currentLevel == 2) {
          return {background: '#eaeb00', color: '#ffffff'}
        } else if (currentLevel == 1) {
          return {background: '#35d206', color: '#ffffff'}
        } else if (currentLevel == 7) {
          return {background: '#bebebe', color: '#ffffff'}
        }
      }
    }
  }
</script>

<style scoped>
  .month-calendar .day-ul {
    width: 315px;
  }

  .month-calendar .day-ul .day-box {
    width: 45px;
    height: 30px;

  }

  .font1 {
    line-height: 12px;
    font-size: 16px;

  }

  .font2 {
    line-height: 28px;
    font-size: 16px;
  }


</style>
