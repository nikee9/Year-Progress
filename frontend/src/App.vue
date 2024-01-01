<template>
  <div id="app">
    <h1>Year Clock</h1>

    <div class="counters">
      <div class="counter">
        <span>Overall:</span>
        <progress id="months" :value="yearProgress" max="100"></progress>
      </div>

      <div class="counter">
        <span>Second:</span>
        <progress
          id="seconds"
          :value="secondProgress || 0"
          max="100"
        ></progress>
      </div>

      <div class="counter">
        <span>Minute:</span>
        <progress id="minutes" :value="minuteProgress" max="100"></progress>
      </div>

      <div class="counter">
        <span>Hour:</span>
        <progress id="hours" :value="hourProgress" max="100"></progress>
      </div>

      <div class="counter">
        <span>Day:</span>
        <progress id="days" :value="dayProgress" max="100"></progress>
      </div>

      <div class="counter">
        <span>Month:</span>
        <progress id="months" :value="monthProgress" max="100"></progress>
      </div>

      <div class="counter">
        <span>Week:</span>
        <progress id="weeks" :value="weekProgress" max="100"></progress>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentSecondInMinute: 0,
      currentMinuteInHour: 0,
      currentHour: 0,
      currentDay: 0,
      currentDaysInMonth: 0,
      currentMonth: 0,
      currentYearWeek: 0,
      currentYearDay: 0,
      currentYear: 0,
      totalDaysInYear: 365,
      totalHoursInYear: 0,
      totalMinutesInYear: 0,
      elapsedHoursInYear: 0,
      elapsedMinutesInYear: 0,
    };
  },
  methods: {
    updateSecond() {
      this.currentSecondInMinute = new Date().getSeconds();
      let now = new Date();
      let delay = 1000 - now.getMilliseconds();
      setTimeout(this.updateSecond, delay);
    },
    updateMinute() {
      this.currentMinuteInHour = new Date().getMinutes();
      let now = new Date();
      let delay = (60 - now.getSeconds()) * 1000 - now.getMilliseconds();
      this.updateElapsedTimes();
      setTimeout(this.updateMinute, delay);
    },
    updateHour() {
      this.currentHour = new Date().getHours();
      let now = new Date();
      let delay = (60 - now.getMinutes()) * 60 * 1000 - now.getMilliseconds();
      this.updateElapsedTimes();
      setTimeout(this.updateHour, delay);
    },
    updateDay() {
      this.currentDay = new Date().getDate();
      let now = new Date();
      let delay =
        (24 - now.getHours()) * 60 * 60 * 1000 - now.getMilliseconds();
      setTimeout(this.updateDay, delay);
    },
    updateMonth() {
      this.currentMonth = new Date().getMonth() + 1;
      let now = new Date();
      let delay = (60 - now.getMinutes()) * 60 * 1000 - now.getMilliseconds();
      setTimeout(this.updateMonth, delay);
    },
    updateDaysInMonth() {
      let date = new Date();
      let month = date.getMonth();
      let year = date.getFullYear();
      this.currentDaysInMonth = new Date(year, month + 1, 0).getDate();

      let now = new Date();
      let delay = (60 - now.getMinutes()) * 60 * 1000 - now.getMilliseconds();
      setTimeout(this.updateDaysInMonth, delay);
    },
    getWeekNumber(d) {
      d = new Date(Date.UTC(d.getFullYear(), d.getMonth(), d.getDate()));
      d.setUTCDate(d.getUTCDate() + 4 - (d.getUTCDay() || 7));
      var startOfYear = new Date(Date.UTC(d.getUTCFullYear(), 0, 1));
      var weekNo = Math.ceil(((d - startOfYear) / 86400000 + 1) / 7);
      return weekNo;
    },
    updateYearWeek() {
      this.currentYearWeek = this.getWeekNumber(new Date());
      let now = new Date();
      let startOfWeek = new Date(
        now.getFullYear(),
        now.getMonth(),
        now.getDate() - now.getDay()
      );
      let delay =
        startOfWeek.getTime() + 7 * 24 * 60 * 60 * 1000 - now.getTime();
      setTimeout(this.updateYearWeek, delay);
    },
    getDayOfYear(d) {
      var start = new Date(d.getFullYear(), 0, 0);
      var diff = d - start;
      var oneDay = 1000 * 60 * 60 * 24;
      var day = Math.floor(diff / oneDay);
      return day;
    },
    updateYearDay() {
      this.currentYearDay = this.getDayOfYear(new Date());
      let now = new Date();
      let startOfDay = new Date(
        now.getFullYear(),
        now.getMonth(),
        now.getDate()
      );
      let delay = startOfDay.getTime() + 24 * 60 * 60 * 1000 - now.getTime();
      setTimeout(this.updateYearDay, delay);
    },
    getTotalDaysInYear(year) {
      return year % 400 === 0 || (year % 100 !== 0 && year % 4 === 0)
        ? 366
        : 365;
    },
    updateYear() {
      let now = new Date();
      this.currentYear = now.getFullYear();
      this.totalDaysInYear = this.getTotalDaysInYear(this.currentYear);
      this.totalHoursInYear = this.totalDaysInYear * 24;
      this.totalMinutesInYear = this.totalHoursInYear * 60;
      let startOfYear = new Date(now.getFullYear(), 0, 1);
      let delay =
        startOfYear.getTime() + 365 * 24 * 60 * 60 * 1000 - now.getTime();
      setTimeout(this.updateYear, delay);
    },
    updateElapsedTimes() {
      let now = new Date();
      let startOfYear = new Date(now.getFullYear(), 0, 1);
      let diff = now - startOfYear;
      this.elapsedHoursInYear = Math.floor(diff / (1000 * 60 * 60));
      this.elapsedMinutesInYear = Math.floor(diff / (1000 * 60));
    },
  },
  computed: {
    secondProgress() {
      return Number((this.currentSecondInMinute / 60) * 100).toFixed(2);
    },
    minuteProgress() {
      return Number((this.currentMinuteInHour / 60) * 100).toFixed(2);
    },
    hourProgress() {
      return Number((this.currentHour / 24) * 100).toFixed(2);
    },
    dayProgress() {
      return Number((this.currentDay / this.currentDaysInMonth) * 100).toFixed(
        2
      );
    },
    monthProgress() {
      return Number((this.currentMonth / 12) * 100).toFixed(2);
    },
    weekProgress() {
      return Number((this.currentYearWeek / 52) * 100).toFixed(2);
    },
    yearProgress() {
      return Number((this.currentYearDay / this.totalDaysInYear) * 100).toFixed(
        2
      );
    },
    yearHourProgress() {
      return Number(
        (this.elapsedHoursInYear / this.totalHoursInYear) * 100
      ).toFixed(2);
    },
    yearMinuteProgress() {
      return Number(
        (this.elapsedMinutesInYear / this.totalMinutesInYear) * 100
      ).toFixed(2);
    },
  },
  mounted() {
    this.updateSecond();
    this.updateMinute();
    this.updateHour();
    this.updateDay();
    this.updateMonth();
    this.updateDaysInMonth();
    this.updateYearWeek();
    this.updateYearDay();
    this.updateYear();
    this.updateElapsedTimes();
  },
};
</script>

<style scoped>
.counters {
  width: 100%;
  display: flex;
  flex-direction: column;
  row-gap: 1rem;
}
.counter {
  display: flex;
  justify-content: center;
  align-items: center;
  column-gap: 0.5rem;
  white-space: nowrap;
}

.counter span {
  width: 180px;
  text-align: left;
}

.counter progress {
  width: 100%;
  height: 40px;
  -webkit-appearance: none;
  appearance: none;
}

.counter progress::-webkit-progress-bar {
  background-color: #eee;
  border-radius: 2px;
}

.counter progress::-webkit-progress-value {
  background-color: #24ff37;
  border-radius: 2px;
}
</style>
