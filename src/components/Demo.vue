<template>
  <div class="container">
    <div v-for="(item, index) in weekDay" :key="index" class="daylist">
      <div class="day">{{ item.day }}</div>
      <div class="toggle">
        <i-switch
          true-color="#8FC325"
          class="switch"
          v-model="item.state"
          @click="saveData()"
        />
        <span v-if="item.state">本日供餐</span>
        <span v-else>本日不供餐</span>
      </div>
      <div v-if="item.state" class="time">
        <Select
          v-model="item.startTime"
          size="large"
          style="width: 200px"
          placeholder="起始時間"
        >
          <Option disabled>請選擇時間</Option>
          <Option
            v-for="item in startTimeList"
            :value="item.value"
            :key="item.value"
            :disabled="isLaterEndTime(item)"
          >
            {{ item.label }}
          </Option>
        </Select>
        <span>-</span>
        <Select
          v-model="item.endTime"
          size="large"
          style="width: 200px"
          placeholder="結束時間"
        >
          <Option disabled>請選擇時間</Option>
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
    <div class="object-list">
      <ul>
        <li v-for="(value, key) in dataObject" :key="key">
          {{ key }}: {{ value }}
        </li>
      </ul>
    </div>
  </div>
</template>
  
  <script>
import { ref, computed, watch, onMounted } from "vue";
import { Switch, Select, Option } from "view-ui-plus";

export default {
  name: "Demo",
  components: {
    Switch,
    Select,
    Option,
  },
  setup() {
    const weekDay = ref([
      {
        day: "星期日",
        state: false,
        startTime: "",
        endTime: "",
      },
      {
        day: "星期一",
        state: true,
        startTime: "",
        endTime: "",
      },
      {
        day: "星期二",
        state: true,
        startTime: "",
        endTime: "",
      },
      {
        day: "星期三",
        state: true,
        startTime: "",
        endTime: "",
      },
      {
        day: "星期四",
        state: true,
        startTime: "",
        endTime: "",
      },
      {
        day: "星期五",
        state: true,
        startTime: "",
        endTime: "",
      },
      {
        day: "星期六",
        state: true,
        startTime: "",
        endTime: "",
      },
    ]);

    const startTimeList = ref([]);
    const endTimeList = ref([]);
    // 循環時間
    for (let hour = 0; hour < 24; hour++) {
      for (let minute = 0; minute < 60; minute += 30) {
        const hourStr = hour.toString().padStart(2, "0");
        const minuteStr = minute.toString().padStart(2, "0");
        const timeValue = `${hourStr}:${minuteStr}`;
        startTimeList.value.push({
          value: timeValue,
          label: timeValue,
        });
      }
    }

    for (let hour = 0; hour < 24; hour++) {
      for (let minute = 0; minute < 60; minute += 30) {
        const hourStr = hour.toString().padStart(2, "0");
        const minuteStr = minute.toString().padStart(2, "0");
        const timeValue = `${hourStr}:${minuteStr}`;
        if (!(hour === 0 && minute === 0)) {
          // 排除00:00
          endTimeList.value.push({
            value: timeValue,
            label: timeValue,
          });
        }
      }
    }

    endTimeList.value.push({
      value: "23:59",
      label: "23:59",
    });

    const startTime = ref("");
    const endTime = ref("");

    weekDay.value.forEach((item) => {
      item.startTime = "00:00";
    });

    weekDay.value.forEach((item) => {
      item.endTime = "23:59";
    });

    // 結束時間不能早於起始時間
    const filterTimeList = (startTime) => {
      return endTimeList.value.map((item) => ({
        ...item,
        disabled: item.value <= startTime,
      }));
    };

    const isLaterEndTime = computed(() => (item) => item.value > item.endTime);

    // 將 startTime 和 endTime 轉為字符串並連接起來
    const timeString = computed(() => {
      const timeData = weekDay.value.map((item) => {
        if (item.state) {
          const startIdx = startTimeList.value.findIndex(
            (timeItem) => timeItem.value === item.startTime
          );
          const endIdx = endTimeList.value.findIndex(
            (timeItem) => timeItem.value === item.endTime
          );
          const timeRange = Array(startTimeList.value.length).fill(0);
          timeRange.fill(1, startIdx, endIdx + 1);
          return timeRange.join("");
        } else {
          return Array(startTimeList.value.length).fill(0).join("");
        }
      });
      return timeData;
    });

    const dataObject = ref({});

    // 儲存資料
    const saveData = () => {
      timeString.value.forEach((str, index) => {
        dataObject.value[`week_day${index}`] = str;
      });
      console.log(dataObject.value);
    };

    // 監聽數據
    watch(
      [
        () => weekDay.value.map((item) => item.startTime),
        () => weekDay.value.map((item) => item.endTime),
      ],
      () => {
        saveData();
      },
      { deep: true }
    );

    // 頁面加載時完成一次saveData()
    onMounted(() => {
      saveData();
    });

    return {
      weekDay,
      startTimeList,
      endTimeList,
      startTime,
      endTime,
      filterTimeList,
      isLaterEndTime,
      timeString,
      dataObject,
      saveData,
    };
  },
};
</script>

<style scoped lang="scss">
$font-family: "Noto Sans CJK TC";
$font-size: 16px;
$text-color: #3e3a39;
.container {
  width: 800px;
  height: auto;
  margin: 100px auto 100px auto;
  .daylist {
    width: 100%;
    height: auto;
    padding: 36px 0;
    margin-bottom: 10px;
    border-bottom: 2px solid #f4f4f4;
    display: flex;
    .day {
      font-family: $font-family;
      font-size: $font-size;
      color: $text-color;
      font-weight: bold;
      margin-right: 40px;
      line-height: 36px;
    }
    .toggle {
      height: 40px;
      display: flex;
      align-items: center;
      margin-right: 62px;
      .switch {
        margin: auto 10px;
      }
      span {
        font-family: $font-family;
        font-size: $font-size;
        color: $text-color;
        font-weight: bold;
        line-height: 36px;
      }
    }
    .time {
      width: 410px;
      height: 40px;
      display: flex;
      justify-content: space-between;
      .time-select {
        width: 192px;
        height: 40px;
        border-radius: 5px;
        background-color: #d9d9d9;
      }
      span {
        font-family: $font-family;
        font-size: $font-size;
        color: $text-color;
        font-weight: bold;
        line-height: 36px;
        margin: 0 10px;
      }
    }
  }
  .object-list {
    text-align: center;
    line-height: 20px;
    li {
      list-style: none;
    }
  }
}
</style>
