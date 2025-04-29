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
        @book-slot="handleBookSlot"
    />
  </div>
</template>

<script>
import { computed } from 'vue'
import { format, addDays } from 'date-fns'
import TimeSlotTable from './TimeSlotTable.vue'

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
    const handleBookSlot = (data) => {
      emit('book-slot', { roomId: props.room.id, ...data })
    }

    return {
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