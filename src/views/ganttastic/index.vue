<script setup lang="ts">
import { ref } from "vue";
import { GGanttChart, GGanttRow } from "@infectoone/vue-ganttastic";

interface BarData {
  beginDate: string;
  endDate: string;
  ganttBarConfig: {
    id: string;
    hasHandles?: boolean;
    label: string;
    style?: Record<string, string>;
  };
}

function formatTime(date: Date) {
  return date.toISOString().substring(11, 16); // HH:mm
}

function generateMonthCheck(startDateStr: string, trainCount = 17): BarData[][] {
  const bars: BarData[][] = [];
  const baseDate = new Date(startDateStr);

  for (let i = 0; i < trainCount; i++) {
    const startDate = new Date(baseDate);
    startDate.setDate(baseDate.getDate() + i * 3); // 每列车间隔3天开始检修
    const day = startDate.getDay(); // 0=周日, 5=周五
    const endDate = new Date(startDate);
    endDate.setDate(startDate.getDate() + (day === 5 ? 3 : 2)); // 周五+3天，其它+2天

    bars.push([
      {
        beginDate: formatTime(startDate),
        endDate: formatTime(endDate),
        ganttBarConfig: {
          id: i.toString(),
          hasHandles: true,
          label: `Train No.${(i + 1).toString().padStart(2, "0")} 检修`,
          style: {
            background: "#5ccfa3"
          }
        }
      }
    ]);
  }

  return bars;
}

const context = ref(generateMonthCheck("2025-04-01"));

const startDateDisplay = ref("2025-04-01");

function refresh() {
  context.value = generateMonthCheck(startDateDisplay.value);
}
</script>

<template>
  <div style="margin-bottom: 1rem;">
    <input type="date" v-model="startDateDisplay" />
    <button @click="refresh">生成月检计划</button>
  </div>

  <g-gantt-chart
    chart-start="00:00"
    chart-end="23:59"
    precision="hour"
    date-format="HH:mm"
    bar-start="beginDate"
    bar-end="endDate"
    grid
  >
    <template #upper-timeunit>
      <h3>{{ `月检起始日：${startDateDisplay}` }}</h3>
    </template>

    <g-gantt-row
      v-for="(item, index) in context"
      :key="index"
      :bars="item"
      :label="`Train No.${(index + 1).toString().padStart(2, '0')}`"
      highlight-on-hover
    />
  </g-gantt-chart>
</template>