<template>
  <div class="room-container">
    <div class="room-info">
      <h3>会议室：{{ room.name }}</h3>
      <p>建议人数：{{ room.capacity }}</p>
      <p>介绍：{{ room.description }}</p>
    </div>

    <TimeSlotTable
        :room="room"
        :current-date="currentDate"
        :time-slots="timeSlots"
        :selected-slots="selectedSlots"
        @select-slot="handleSelectSlot"
        @book-slot="handleBookSlot"
    />
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import { format, addDays } from 'date-fns'
import TimeSlotTable from './TimeSlotTable.vue'

// 模拟当前用户
const currentUser = '张三'

export default {
  components: { TimeSlotTable },
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
    const selectedSlots = ref(new Set())

    const handleSelectSlot = (time) => {
      if (selectedSlots.value.has(time)) {
        const formattedDate = format(props.currentDate, 'yyyy-MM-dd')
        const isBooked = props.room.bookings[formattedDate]?.[time]
        if (isBooked) {
          if (props.room.bookings[formattedDate][time] === currentUser) {
            alert('这是您已预定的时段！')
          } else {
            alert('这是他人已预定的时段！')
          }
        } else {
          const confirmBook = confirm(`确定要预定 ${formattedDate} ${time} 这个时段吗？`)
          if (confirmBook) {
            emit('book-slot', { date: formattedDate, time })
          }
        }
        selectedSlots.value.delete(time)
      } else {
        selectedSlots.value.add(time)
      }
    }

    const handleBookSlot = (data) => {
      emit('book-slot', { roomId: props.room.id, ...data })
    }

    return {
      selectedSlots,
      handleSelectSlot,
      handleBookSlot
    }
  }
}
</script>

<style scoped>
.room-container {
  background-color: white;
  padding: 15px;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}
.room-info {
  margin-bottom: 15px;
}
.room-info h3 {
  margin: 0 0 5px 0;
}
.room-info p {
  margin: 5px 0;
  color: #666;
}
</style>