<template>
  <div class="room-booking">
    <div class="header">
      <h1>会议室预定</h1>
      <div class="action-buttons">
        <button @click="handleBooking">预定</button>
        <button @click="handleRecords">预定记录</button>
        <div class="button-with-color">
          <span class="color-block selected-not-booked-color"></span>
          <button @click="handleDaily">已选择但未预定</button>
        </div>
        <div class="button-with-color">
          <span class="color-block booked-by-others-color"></span>
          <button @click="handleReport">已预定（别人的）</button>
        </div>
        <div class="button-with-color">
          <span class="color-block my-booking-color"></span>
          <button @click="handleDate">我的预订</button>
        </div>
        <div class="button-with-color">
          <span class="color-block not-booked-color"></span>
          <button>未预定</button>
        </div>
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

// 模拟当前用户
const currentUser = '张三'

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
          '2025-05-01': { '8:00': '张三', '11:00': '赵六', '15:00': '王老七' },
          '2025-05-02': { '10:00': '李四', '14:00': '张三' },
          '2025-05-03': { '12:00': '王五' }
        }
      },
      {
        id: 2,
        name: '会议室2',
        capacity: 20,
        description: '支持换屏，电话会议',
        bookings: {
          '2025-05-01': { '9:00': '孙七', '13:00': '周八', '16:00': '吴九' },
          '2025-05-02': { '11:00': '郑十', '15:00': '陈十一' },
          '2025-05-03': { '13:00': '林十二' }
        }
      },
      {
        id: 3,
        name: '会议室3',
        capacity: 20,
        description: '支持换屏，电话会议',
        bookings: {
          '2025-05-01': { '10:00': '胡十三', '14:00': '朱十四', '17:00': '徐十五' },
          '2025-05-02': { '12:00': '吕十六', '16:00': '施十七' },
          '2025-05-03': { '14:00': '张十八' }
        }
      }
    ])

    // 时间槽
    const timeSlots = ref([
      '8:00', '9:00', '10:00', '11:00', '12:00',
      '13:00', '14:00', '15:00', '16:00', '17:00', '18:00'
    ])

    // 修改当前日期为现实时间
    const currentDate = ref(new Date())

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

      if (!room.bookings[date]) {
        room.bookings[date] = {}
      }
      room.bookings[date][time] = currentUser
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
.button-with-color {
  display: flex;
  align-items: center;
  gap: 5px;
}

.color-block {
  width: 15px;
  height: 15px;
  border-radius: 3px;
}

.selected-not-booked-color {
  background-color: #ffeb3b; /* 黄色 */
}

.booked-by-others-color {
  background-color: #f44336; /* 红色 */
}

.my-booking-color {
  background-color: #2196f3; /* 蓝色 */
}

.not-booked-color {
  background-color: #4CAF50; /* 绿色 */
}

/* 假设 RoomCard 组件中有时间槽元素，可根据预订情况添加颜色 */
.room-card .time-slot {
  cursor: pointer;
  padding: 5px;
  margin: 2px;
  border-radius: 3px;
}

.room-card .time-slot.selected-not-booked {
  background-color: #ffeb3b;
}

.room-card .time-slot.booked-by-others {
  background-color: #f44336;
  color: white;
}

.room-card .time-slot.my-booking {
  background-color: #2196f3;
  color: white;
}

.room-card .time-slot.not-booked {
  background-color: #4CAF50;
}
</style>
<mcfile name="RoomBooking.vue" path="d:\siyouyun\最终项目\bookingroom\src\components\RoomBooking.vue"></mcfile>