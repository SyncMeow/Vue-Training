<template>
  <div class="p-6">
    <h1 class="text-2xl font-bold mb-4">Class Schedule</h1>
    <div class="overflow-x-auto">
      <table class="border-collapse border border-gray-300 w-full">
        <thead>
          <tr>
            <th class="border border-gray-300 p-2">節數</th>
            <th v-for="day in days" :key="day" class="border border-gray-300 p-2">{{ day }}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, rowIndex) in scheduleGrid" :key="rowIndex">
            <td class="border border-gray-300 p-2 text-center">{{ rowIndex + 1 }}</td>
            <td v-for="(cell, colIndex) in row" :key="colIndex"
                :class="['border border-gray-300 p-2 text-center', cell ? getColor(cell.subject) : 'bg-white']">
              <div v-if="cell">
                <strong>{{ cell.subject }}</strong><br />
                {{ cell.location }}
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const days = ['M', 'T', 'W', 'R', 'F'];

// Define a color map for subjects
const colorMap = {
  'PE': 'bg-green-200',
  '離散數學': 'bg-red-200',
  '數位電路設計': 'bg-blue-200',
  '積體電路': 'bg-yellow-200',
  '資料結構與物件導向程式設計': 'bg-purple-200',
  '日文(一)': 'bg-gray-300',
  '物理(二)': 'bg-orange-200',
  '生活規劃': 'bg-pink-200',
  '微積分甲(二)': 'bg-teal-200'
};

const getColor = (subject) => colorMap[subject] || 'bg-gray-100';

// Schedule Grid (2D array)
const scheduleGrid = ref([
  [null, null, { subject: 'PE', location: 'GF' }, null, null],
  [null, null, null, null, null],
  [{ subject: '離散數學', location: '122[GF]' }, null, { subject: '數位電路設計', location: '117[GF]' }, null, null],
  [null, null, null, { subject: '積體電路', location: '201[GF]' }, null],
  [{ subject: '資料結構與物件導向程式設計', location: '114[GF]' }, { subject: '日文(一)', location: '214[GF]' }, null, null, null],
  [null, { subject: '物理(二)', location: '001[GF]' }, { subject: '生活規劃', location: '122[GF]' }, null, null],
  [{ subject: '微積分甲(二)', location: '201[GF]' }, null, null, null, { subject: '數位電路設計', location: '117[GF]' }],
  [null, null, null, null, null],
  [null, null, null, null, null],
  [null, null, null, null, null],
]);
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
}
th, td {
  text-align: center;
  padding: 10px;
  font-weight: bold;
}
</style>
