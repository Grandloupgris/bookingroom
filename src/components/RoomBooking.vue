<template>
  <div class="room-booking">
    <div class="header">
      <h1>会议室预定</h1>
      <div class="action-buttons">
        <button @click="handleBooking">预定</button>
        <button @click="handleRecords">预定记录</button>
        <button @click="handleDaily">已预定</button>
        <button @click="handleReport">我的预订</button>
        <button @click="handleDate">日期定</button>
      </div>
    </div>

    <div class="room-filter">
      <label for="room-select">会议室：</label>
      <select id="room-select" v-model="selectedRoom">
        <option value="all">全部</option>
        <option v-for="room in rooms" :key="room.id" :value="room.id">
          会议室{{ room.id }}
        </option>
      </select>
    </div>

    <div class="date-navigation">
      <button @click="prevDay">前一天</button>
      <span>{{ formattedCurrentDate }}</span>
      <button @click="nextDay">后一天</button>
    </div>

    <div class="rooms-container">
      <RoomCard
          v-for="room in filteredRooms"
          :key="room.id"
          :room="room"
          :current-date="currentDate"
          :time-slots="timeSlots"
          @book-slot="handleBookSlot"
      />
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import { format } from 'date-fns'
import RoomCard from './RoomCard.vue'

export default {
  components: { RoomCard },
  setup() {
    // 会议室数据
    const rooms = ref([
      {
        id: 1,
        name: '会议室1',
        capacity: 20,
        description: '支持换屏，电话会议',
        bookings: {
          '2022-05-01': { '8:00': '张三', '10:00': '赵六', '14:00': '王老七' },
          '2022-05-02': { '9:00': '李四', '13:00': '张三' },
          '2022-05-03': { '11:00': '王五' }
        }
      },
      {
        id: 2,
        name: '会议室2',
        capacity: 20,
        description: '支持换屏，电话会议',
        bookings: {
          '2022-05-01': { '8:00': '张三', '10:00': '赵六', '14:00': '王老七' },
          '2022-05-02': { '9:00': '李四', '13:00': '张三' },
          '2022-05-03': { '11:00': '王五' }
        }
      },
      {
        id: 3,
        name: '会议室3',
        capacity: 20,
        description: '支持换屏，电话会议',
        bookings: {
          '2022-05-01': { '8:00': '张三', '10:00': '赵六', '14:00': '王老七' },
          '2022-05-02': { '9:00': '李四', '13:00': '张三' },
          '2022-05-03': { '11:00': '王五' }
        }
      }
    ])

    // 时间槽
    const timeSlots = ref([
      '8:00', '9:00', '10:00', '11:00', '12:00',
      '13:00', '14:00', '15:00', '16:00', '17:00', '18:00'
    ])

    // 当前日期
    const currentDate = ref(new Date('2022-05-01'))
    const selectedRoom = ref('all')

    // 格式化当前日期
    const formattedCurrentDate = computed(() => {
      return format(currentDate.value, 'yyyy-MM-dd')
    })

    // 过滤显示的会议室
    const filteredRooms = computed(() => {
      if (selectedRoom.value === 'all') {
        return rooms.value
      }
      return rooms.value.filter(room => room.id === parseInt(selectedRoom.value))
    })

    // 日期导航
    const prevDay = () => {
      const newDate = new Date(currentDate.value)
      newDate.setDate(newDate.getDate() - 1)
      currentDate.value = newDate
    }

    const nextDay = () => {
      const newDate = new Date(currentDate.value)
      newDate.setDate(newDate.getDate() + 1)
      currentDate.value = newDate
    }

    // 处理预定
    const handleBookSlot = ({ roomId, date, time }) => {
      const room = rooms.value.find(r => r.id === roomId)
      if (!room) return

      const booker = prompt(`预定会议室${roomId} ${date} ${time}\n请输入预定人姓名:`)
      if (booker) {
        if (!room.bookings[date]) {
          room.bookings[date] = {}
        }
        room.bookings[date][time] = booker
      }
    }

    // 按钮处理函数
    const handleBooking = () => console.log('预定')
    const handleRecords = () => console.log('预定记录')
    const handleDaily = () => console.log('日录表')
    const handleReport = () => console.log('报表定')
    const handleDate = () => console.log('日期定')

    return {
      rooms,
      timeSlots,
      currentDate,
      selectedRoom,
      formattedCurrentDate,
      filteredRooms,
      prevDay,
      nextDay,
      handleBookSlot,
      handleBooking,
      handleRecords,
      handleDaily,
      handleReport,
      handleDate
    }
  }
}
</script>

<style scoped>
.room-booking {
  font-family: 'Microsoft YaHei', sans-serif;
  margin: 0;
  padding: 20px;
  background-color: #f5f5f5;
}
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  padding-bottom: 10px;
  border-bottom: 1px solid #ddd;
}
.room-filter {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
}
.date-navigation {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
}
.date-navigation span {
  margin: 0 10px;
  font-weight: bold;
}
.action-buttons {
  display: flex;
  gap: 10px;
}
button {
  padding: 5px 10px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}
button:hover {
  background-color: #45a049;
}
.rooms-container {
  display: flex;
  flex-direction: column;
  gap: 30px;
}
</style>