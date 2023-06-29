<script setup lang="ts">
  import { ref } from 'vue';

  interface DayProps {
    currentMonth: boolean;
    date: Date;
    month: number;
    dayNumber: number;
    year: number
  }

  const today = new Date();

  const daySelected = ref(
    new Date(
      today.getFullYear() - 1, today.getMonth(), 1
    )
  );

  // the day we start the month
  const firstDayOfMonth = new Date(today.getFullYear(), today.getMonth(), 1);

  // the day of the week
  const weekdayNumber = firstDayOfMonth.getDay();

  const daysOfTheMonth = new Array<DayProps>()

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

    daysOfTheMonth.push({
      currentMonth: (firstDayOfMonth.getMonth() === today.getMonth()),
      date: (new Date(firstDayOfMonth)),
      month: firstDayOfMonth.getMonth(),
      dayNumber: firstDayOfMonth.getDate(),
      // selected: (firstDayOfMonth.toDateString() === today.toDateString()),
      year: firstDayOfMonth.getFullYear()
    });
  }
  
  const calendarGrid = [
    ...Array(Math.ceil(daysOfTheMonth.length / 7))
  ].map(_ => daysOfTheMonth.splice(0,7));

  const isCurrentDay = (day: number, weekIndex: number) => {
    return (
      day === today.getDate() &&
      Math.ceil(today.getDate() / 7) === weekIndex + 1
    )
  }

  const handleDaySelection = (dayProps: DayProps) => {
    daySelected.value = new Date(
      dayProps.year,
      dayProps.month,
      dayProps.dayNumber
    );
  }

  const getComputedClassesForDay = (day: DayProps, indexOfWeek: number) => {
    return {
      'current-day': isCurrentDay(day.dayNumber, indexOfWeek),
      'selected-day': day.date.toDateString() === daySelected?.value.toDateString()
    }
  }

</script>
<template>
  <div class="calendar-container">
    <div class="calendar-controls-header">
      <p>
        {{ (today.toLocaleString('en-us', { month:'long', year:'numeric' })) }}
      </p>
      <div class="calendar-controls">
          <div class="tooltip">
            &lt;
            <span class="tooltiptext">previous month</span>
          </div>
          <div class="tooltip">
            <span>&gt;</span>
            <span class="tooltiptext">next month</span>
          </div>
      </div>
    </div>
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
          :key="day.dayNumber"
          class="week-day"
          :class="getComputedClassesForDay(day, indexOfWeek)"
          v-on:click="() => handleDaySelection(day)"
        >
          {{ day.dayNumber }}
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
  
  .calendar-controls-header {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    margin-bottom: 10px;
  }

  .calendar-controls {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    width: 50px;
    align-self: center;
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

  .selected-day {
    background-color: #3498db;
    border-radius: 50%;
    padding: 10px;
    color: white;
  }

  .tooltip {
    position: relative;
    display: inline-block;
  }
  .tooltip .tooltiptext {
    visibility: hidden;
    background-color: black;
    color: white;
    border-radius: 7px;
    padding: 5px 10px;
    position: absolute;
    z-index: 1;
    top: 100%;
    left: 60%;
    text-align: center;
    margin-left: -65px;
    width: 100px;
  }

  .tooltip:hover .tooltiptext {
    visibility: visible;
  }
</style>
