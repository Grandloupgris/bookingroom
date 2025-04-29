<template>
  <table class="time-table">
    <thead>
    <tr>
      <th>时间/日期</th>
      <th v-for="time in timeSlots" :key="time">{{ time }}</th>
    </tr>
    </thead>
    <tbody>
    <tr v-for="day in displayedDays" :key="day.date">
      <td>{{ day.label }}</td>
      <td
          v-for="time in timeSlots"
          :key="time"
          :class="{'booked': isBooked(day.date, time), 'available': !isBooked(day.date, time)}"
          @click="!isBooked(day.date, time) && handleSlotClick(day.date, time)"
      >
        <div class="time-slot">
          <div v-if="isBooked(day.date, time)" class="booking-info">
            {{ getBooker(day.date, time) }}
          </div>
        </div>
      </td>
    </tr>
    </tbody>
  </table>
</template>

<script>
import { computed } from 'vue'
import { format, addDays } from 'date-fns'

export default {
  props: {
    room: {
      type: Object,
      required: true
    },
    currentDate: {
      type: Date,
      required: true
    },
    timeSlots: {
      type: Array,
      required: true
    }
  },
  emits: ['book-slot'],
  setup(props, { emit }) {
    // 显示3天的数据
    const displayedDays = computed(() => {
      return [0, 1, 2].map(offset => {
        const date = addDays(props.currentDate, offset)
        return {
          date: format(date, 'yyyy-MM-dd'),
          label: `${date.getMonth() + 1}月${date.getDate()}日`
        }
      })
    })

    // 检查时间段是否被预定
    const isBooked = (date, time) => {
      return props.room.bookings[date] && props.room.bookings[date][time]
    }

    // 获取预定人
    const getBooker = (date, time) => {
      return props.room.bookings[date]?.[time] || ''
    }

    // 处理时间段点击
    const handleSlotClick = (date, time) => {
      emit('book-slot', { date, time })
    }

    return {
      displayedDays,
      isBooked,
      getBooker,
      handleSlotClick
    }
  }
}
</script>

<style scoped>
.time-table {
  width: 100%;
  border-collapse: collapse;
}
.time-table th {
  background-color: #f2f2f2;
  padding: 8px;
  text-align: center;
  border: 1px solid #ddd;
}
.time-table td {
  padding: 8px;
  border: 1px solid #ddd;
  text-align: center;
  position: relative;
}
.time-slot {
  position: relative;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.booked {
  background-color: #ffdddd;
}
.available {
  background-color: #e6ffe6;
  cursor: pointer;
}
.available:hover {
  background-color: #ccffcc;
}
.booking-info {
  font-size: 12px;
}
</style>