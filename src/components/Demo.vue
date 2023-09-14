<template>
    <div class="container">
      <div 
        v-for="(item, index) in weekDay"
        :key="index"
        class="daylist"
      >
          <div class="day">{{ item.day }}</div>
          <div class="toggle">
              <i-switch
              true-color="#8FC325"
              class="switch"
              v-model="item.state"
              />
              <span v-if="item.state">本日供餐</span>
              <span v-else>本日不供餐</span>
          </div>
          <div v-if="item.state" class="time">
            <Select 
            size="large"
            style="width:200px"
            >
                <Option v-for="item in timeList" :value="item.value" :key="item.value">{{ item.label }}</Option>
            </Select>
            <span>-</span>
            <Select 
            size="large"
            style="width:200px"
            >
                <Option v-for="item in timeList" :value="item.value" :key="item.value">{{ item.label }}</Option>
            </Select>
          </div>
      </div>
    </div>
  </template>
  
  <script>
  import { ref } from 'vue'
  import { Switch, Select, Option } from 'view-ui-plus';
  
  export default {
      name: 'Demo',
      components:{
          Switch,
          Select,
          Option
      },
      setup() {
          const weekDay = ref([
              {
                  day: '星期日',
                  state: true
              },
              {
                  day: '星期一',
                  state: true
              },
              {
                  day: '星期二',
                  state: true
              },
              {
                  day: '星期三',
                  state: true
              },
              {
                  day: '星期四',
                  state: true
              },
              {
                  day: '星期五',
                  state: true
              },
              {
                  day: '星期六',
                  state: true
              },
          ])

          const timeList = ref([
              {
                value: '00 : 00',
                label: '00 : 00'
              },
              {
                value: '01 : 00',
                label: '01 : 00'
              },
              {
                value: '02 : 00',
                label: '02 : 00'
              },
              {
                value: '03 : 00',
                label: '03 : 00'
              },
              {
                value: '04 : 00',
                label: '04 : 00'
              },
              {
                value: '05 : 00',
                label: '05 : 00'
              },
              {
                value: '06 : 00',
                label: '06 : 00'
              },
          ])

          return {
              weekDay,
              timeList
          }
      }
  }
  </script>

<style scoped lang="scss">
  $font-family: "Noto Sans CJK TC";
  $font-size: 16px;
  $text-color: #3E3A39;
    .container {
        width: 800px;
        height: auto;
        margin: 100px auto 0 auto;
        // background: #eeeeee;
        .daylist {
            width: 100%;
            height: auto;
            padding: 36px 0;
            border-bottom: 2px solid #f4f4f4;
            display: flex;
            .day{
                font-family: $font-family;
                font-size: $font-size;
                color: $text-color;
                font-weight: bold;
                margin-right: 40px;
                line-height: 36px;
            }
            .toggle{
                height: 40px;
                display: flex;
                align-items: center;
                margin-right: 62px;
                .switch{
                    margin: auto 10px;
                }
                span{
                    font-family: $font-family;
                    font-size: $font-size;
                    color: $text-color;
                    font-weight: bold;
                    line-height: 36px;
                }
            }
            .time{
                width: 410px;
                height: 40px;
                display: flex;
                justify-content: space-between;
                .time-select{
                    width: 192px;
                    height: 40px;
                    border-radius: 5px;
                    background-color: #d9d9d9;
                }
                span{
                    font-family: $font-family;
                    font-size: $font-size;
                    color: $text-color;
                    font-weight: bold;
                    line-height: 36px;
                    margin: 0 10px;
                }
            }
        }
    }
</style>
