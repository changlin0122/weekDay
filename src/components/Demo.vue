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
            v-model="item.startTime"
            size="large"
            style="width:200px"
            placeholder="起始時間"
            >
                <Option disabled>請選擇時間</Option>
                <Option 
                v-for="item in timeList" 
                :value="item.value" 
                :key="item.value"
                >
                    {{ item.label }}
                </Option>
            </Select>
            <span>-</span>
            <Select 
            v-model="item.endTime"
            size="large"
            style="width:200px"
            placeholder="結束時間"
            >
                <Option disabled >請選擇時間</Option>
                <Option 
                v-for="item in filterTimeList(item.startTime)" 
                :value="item.value" 
                :key="item.value"
                :disabled="item.disabled"
                >
                {{ item.label }}
            </Option>
            </Select>
          </div>
      </div>
      <button @click="saveData">保存数据</button>
      <div>{{ dataObject }}</div>
    </div>
  </template>
  
  <script>
  import { ref, computed, watch } from 'vue'
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
                state: true,
                startTime: '',
                endTime: ''
            },
            {
                day: '星期一',
                state: true,
                startTime: '',
                endTime: ''
            },
            {
                day: '星期二',
                state: true,
                startTime: '',
                endTime: ''
            },
            {
                day: '星期三',
                state: true,
                startTime: '',
                endTime: ''
            },
            {
                day: '星期四',
                state: true,
                startTime: '',
                endTime: ''
            },
            {
                day: '星期五',
                state: true,
                startTime: '',
                endTime: ''
            },
            {
                day: '星期六',
                state: true,
                startTime: '',
                endTime: ''
            },
            
        ]);

        const timeList = ref([])
          // 循環時間
          for (let hour = 0; hour < 24; hour++) {
            for (let minute = 0; minute < 60; minute += 30) {
                const hourStr = hour.toString().padStart(2, '0');
                const minuteStr = minute.toString().padStart(2, '0');
                const timeValue = `${hourStr} : ${minuteStr}`;
                timeList.value.push({
                value: timeValue,
                label: timeValue,
                });
            }
        }

        const startTime = ref('')
        const endTime = ref('')

        // 結束時間不能早於起始時間
        const filterTimeList = (startTime) => {
            return timeList.value.map((item) => ({
                ...item,
                disabled: item.value <= startTime,
            }));
        };

        // 將 startTime 和 endTime 轉為字符串並連接起來
        const timeString = computed(() => {
            const timeData = weekDay.value.map((item) => {
                if (item.state) {
                    const startIdx = timeList.value.findIndex((timeItem) => timeItem.value === item.startTime)
                    const endIdx = timeList.value.findIndex((timeItem) => timeItem.value === item.endTime)
                    const timeRange = Array(timeList.value.length).fill(0)
                    timeRange.fill(1, startIdx, endIdx + 1)
                    return timeRange.join('') 
                } else {
                    return Array(timeList.value.length).fill(0).join('')
                }
            })
            return timeData
        })

        // 儲存資料
        const saveData = () => {
            const dataObject = {}
            timeString.value.forEach((str, index) => {
                dataObject[`week_day${index}`] = str
            })
            console.log(dataObject)
        }

        // 監聽數據
        watch([weekDay, startTime, endTime], () => {
            const allDaysHaveTime = weekDay.value.every((day) => day.startTime && day.endTime)
            if (allDaysHaveTime) {
                saveData();
            }
        })

        return {
            weekDay,
            timeList,
            startTime,
            endTime,
            filterTimeList,
            timeString,
            saveData
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
