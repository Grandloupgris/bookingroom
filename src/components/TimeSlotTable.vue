<template>
  <div>
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
            :class="{
              'booked': isBooked(day.date, time),
              'available': !isBooked(day.date, time),
              'selected': selectedSlots.has(`${day.date}-${time}`),
              'booked-by-others': isBookedByOthers(day.date, time),
              'my-booking': isMyBooking(day.date, time)
            }"
            @click="handleSlotClick(day.date, time)"
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
  </div>
</template>

<script>
import { computed, ref } from 'vue'
import { format, addDays } from 'date-fns'

// 模拟当前用户
const currentUser = '张三'

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
  emits: ['book-slot', 'select-slot'],
  setup(props, { emit }) {
    const selectedSlots = ref(new Set())

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

    const isBookedByOthers = (date, time) => {
      return props.room.bookings[date]?.[time] && props.room.bookings[date][time] !== currentUser
    }

    const isMyBooking = (date, time) => {
      return props.room.bookings[date]?.[time] === currentUser
    }

    // 处理时间段点击
    const handleSlotClick = (date, time) => {
      const key = `${date}-${time}`
      if (selectedSlots.value.has(key)) {
        if (isBooked(date, time)) {
          if (isMyBooking(date, time)) {
            alert('这是您已预定的时段！')
          } else {
            alert('这是他人已预定的时段！')
          }
        } else {
          const confirmBook = confirm(`确定要预定 ${date} ${time} 这个时段吗？`)
          if (confirmBook) {
            emit('book-slot', { date, time })
          }
        }
        selectedSlots.value.delete(key)
      } else {
        if (!isBooked(date, time)) {
          selectedSlots.value.add(key)
          emit('select-slot', { date, time })
        }
      }
    }

    return {
      displayedDays,
      isBooked,
      getBooker,
      isBookedByOthers,
      isMyBooking,
      selectedSlots,
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
  /* 可移除该样式，因为已由更具体的类控制 */
  background-color: initial; 
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
.time-slot.selected {
  background-color: #ffeb3b; /* 黄色，第一次点击后未预定 */
}
.time-slot.booked-by-others {
  background-color: #f44336; /* 红色，非本用户预定 */
  color: white;
}
.time-slot.my-booking {
  background-color: #2196f3; /* 蓝色，本用户预定 */
  color: white;
}
</style>