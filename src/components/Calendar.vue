<script setup lang="ts">
  import { ref } from 'vue';
  const today = new Date();

  // the day we start the month
  const firstDayOfMonth = new Date(today.getFullYear(), today.getMonth(), 1);

  // the day of the week
  const weekdayNumber = firstDayOfMonth.getDay();

  const daysOfTheMonth = new Array<number>()

  // 42 days to roll back and into the next month
  // a month is over 4 weeks and a 'couple' days with 7 days each week 5 * 7 = 35
  // but wee need more than 35 days to in order to be able to roll back into the past month and into the next
  // Also we need 'room' at the beginning of the month to start the month
  for (let index = 0; index < 42; index++) {
    if (index === 0 && weekdayNumber === 0) {
      firstDayOfMonth.setDate(firstDayOfMonth.getDate() - 7);
    } else if (index === 0) {
      firstDayOfMonth.setDate(firstDayOfMonth.getDate() + (index - weekdayNumber));
    } else {
      firstDayOfMonth.setDate(firstDayOfMonth.getDate() + 1);
    }
    daysOfTheMonth.push(firstDayOfMonth.getDate())
  }
  
  const calendarGrid = [
    ...Array(Math.ceil(daysOfTheMonth.length / 7))
  ].map(_ => daysOfTheMonth.splice(0,7));

  const isCurrentDay = (day: number, weekIndex: number) => {
    return (
      day === (new Date()).getDate() && 
      Math.ceil(new Date().getDate() / 7) === weekIndex + 1
    )
  }
  
</script>
<template>
  <div class="calendar-container">
    <div class="calendar-week-days-header">
      <div
        v-for="day in ['S', 'M', 'T', 'W', 'T', 'F', 'S']"
        :key="day"
        class="week-day"
      >
        {{ day }}
      </div>
    </div>
    <div>
      <div
        v-for="(week, indexOfWeek) in calendarGrid"
        :key="week.join('-')"
        class="calendar-week"
      >
        <div
          v-for="day in week"
          :key="day"
          class="week-day"
          :class="{'current-day': isCurrentDay(day, indexOfWeek)}"
        >
          {{ day }}
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .calendar-container {
    display: flex;
    flex-direction: column;
    width: 50%;
    margin: 20px;
  }
  .calendar-week-days-header {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    margin-bottom: 15px;
  }
  .calendar-week {
    display: flex;
    flex-direction: row;
    height: 50px;
    justify-content: space-between;
  }
  .week-day {
    display: flex;
    justify-content: center;
    width: 20px;
    height: 20px;
    padding: 10px;
  }
  .week-day:hover {
    cursor: pointer;
    /* color: red; */
    background-color: #ecf0f1;
    border-radius: 50%;
  }
  .current-day {
    background-color: #2980b9;
    border-radius: 50%;
    padding: 10px;
    color: white;
  }
</style>
