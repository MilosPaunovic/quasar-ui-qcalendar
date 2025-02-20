<template>
  <div class="subcontent">
    <navigation-bar
      @today="onToday"
      @prev="onPrev"
      @next="onNext"
    />

    <div style="display: flex; justify-content: center; align-items: center;">
      <q-checkbox
        v-model="mobile"
        label="Mobile selection (first click, second click)"
      />
    </div>

    <div class="row justify-center">
      <div style="display: flex; max-width: 800px; width: 100%;">
        <q-calendar-month
          ref="calendar"
          v-model="selectedDate"
          :selected-start-end-dates="startEndDates"
          no-active-date
          :day-min-height="40"
          animated
          bordered
          @mousedown-day="onMouseDownDay"
          @mouseup-day="onMouseUpDay"
          @mousemove-day="onMouseMoveDay"
          @change="onChange"
          @moved="onMoved"
          @click-date="onClickDate"
          @click-workweek="onClickWorkweek"
          @click-head-workweek="onClickHeadWorkweek"
          @click-head-day="onClickHeadDay"
        />
      </div>
    </div>
  </div>
</template>

<script>
import {
  QCalendarMonth,
  getDayIdentifier,
  today
} from '@quasar/quasar-ui-qcalendar/src/index.js'
import '@quasar/quasar-ui-qcalendar/src/QCalendarVariables.sass'
import '@quasar/quasar-ui-qcalendar/src/QCalendarTransitions.sass'
import '@quasar/quasar-ui-qcalendar/src/QCalendarMonth.sass'

import { defineComponent, ref, computed } from 'vue'
import NavigationBar from '../components/NavigationBar.vue'

function leftClick (e) {
  return e.button === 0
}

export default defineComponent({
  name: 'MonthSelection',
  components: {
    NavigationBar,
    QCalendarMonth
  },
  setup () {
    const selectedDate = ref(today()),
      calendar = ref(null),
      anchorTimestamp = ref(null),
      otherTimestamp = ref(null),
      mouseDown = ref(false),
      mobile = ref(false)

    const startEndDates = computed(() => {
      const dates = []
      if (anchorDayIdentifier.value !== false && otherDayIdentifier.value !== false) {
        if (anchorDayIdentifier.value <= otherDayIdentifier.value) {
          dates.push(anchorTimestamp.value.date, otherTimestamp.value.date)
        }
        else {
          dates.push(otherTimestamp.value.date, anchorTimestamp.value.date)
        }
      }
      return dates
    })

    const anchorDayIdentifier = computed(() => {
      if (anchorTimestamp.value !== null) {
        return getDayIdentifier(anchorTimestamp.value)
      }
      return false
    })

    const otherDayIdentifier = computed(() => {
      if (otherTimestamp.value !== null) {
        return getDayIdentifier(otherTimestamp.value)
      }
      return false
    })

    function onMouseDownDay ({ scope, event }) {
      if (leftClick(event)) {
        if (mobile.value === true
          && anchorTimestamp.value !== null
          && otherTimestamp.value !== null
          && anchorTimestamp.value.date === otherTimestamp.value.date) {
          otherTimestamp.value = scope.timestamp
          mouseDown.value = false
          return
        }
        // mouse is down, start selection and capture current
        mouseDown.value = true
        anchorTimestamp.value = scope.timestamp
        otherTimestamp.value = scope.timestamp
      }
    }

    function onMouseUpDay ({ scope, event }) {
      if (leftClick(event)) {
        // mouse is up, capture last and cancel selection
        otherTimestamp.value = scope.timestamp
        mouseDown.value = false
      }
    }

    function onMouseMoveDay ({ scope, event }) {
      if (mouseDown.value === true && scope.outside !== true) {
        otherTimestamp.value = scope.timestamp
      }
    }

    function onToday () {
      calendar.value.moveToToday()
    }
    function onPrev () {
      calendar.value.prev()
    }
    function onNext () {
      calendar.value.next()
    }
    function onMoved (data) {
      console.log('onMoved', data)
    }
    function onChange (data) {
      console.log('onChange', data)
    }
    function onClickDate (data) {
      console.log('onClickDate', data)
    }
    // function onClickDay (data) {
    //   console.log('onClickDay', data)
    // }
    function onClickWorkweek (data) {
      console.log('onClickWorkweek', data)
    }
    function onClickHeadDay (data) {
      console.log('onClickHeadDay', data)
    }
    function onClickHeadWorkweek (data) {
      console.log('onClickHeadWorkweek', data)
    }

    return {
      selectedDate,
      calendar,
      mobile,
      startEndDates,
      onMouseDownDay,
      onMouseUpDay,
      onMouseMoveDay,
      onToday,
      onPrev,
      onNext,
      onMoved,
      onChange,
      onClickDate,
      onClickWorkweek,
      onClickHeadDay,
      onClickHeadWorkweek
    }
  }
})
</script>
