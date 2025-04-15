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

function generateMonthCheck(startDateStr: string, trainCount = 17): BarData[][] {
  const bars: BarData[][] = [];
  const baseDate = new Date(startDateStr);

  for (let i = 0; i < trainCount; i++) {
    const beginDate = new Date(baseDate);
    beginDate.setDate(baseDate.getDate() + i * 3); // 间隔3天检查

    const dayOfWeek = beginDate.getDay(); // 周五为5
    const duration = dayOfWeek === 5 ? 3 : 2;

    const endDate = new Date(beginDate);
    endDate.setDate(beginDate.getDate() + duration - 1);

    const format = (d: Date) => d.toISOString().split("T")[0];

    bars.push([
      {
        beginDate: format(beginDate),
        endDate: format(endDate),
        ganttBarConfig: {
          id: `${i}`,
          hasHandles: true,
          label: `月检计划 负责人：员工${i + 1}`,
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
const trainLabels = Array.from({ length: 17 }, (_, i) => `Train No.${String(i + 1).padStart(2, "0")}`);

// 增加
const addBar = () => {
  const index = context.value.length;
  const newDate = new Date("2025-05-01");
  newDate.setDate(newDate.getDate() + index * 3);
  const day = newDate.getDay();
  const duration = day === 5 ? 3 : 2;

  const endDate = new Date(newDate);
  endDate.setDate(newDate.getDate() + duration - 1);

  const format = (d: Date) => d.toISOString().split("T")[0];

  context.value.push([
    {
      beginDate: format(newDate),
      endDate: format(endDate),
      ganttBarConfig: {
        id: `${index}`,
        hasHandles: true,
        label: `新增计划 负责人：新员工${index + 1}`,
        style: {
          background: "#e96560"
        }
      }
    }
  ]);
  trainLabels.push(`Train No.${String(index + 1).padStart(2, "0")}`);
};

// 删除
const deleteBar = () => {
  if (context.value.length > 0) {
    context.value.pop();
    trainLabels.pop();
  }
};

// 修改
const editBar = () => {
  if (context.value.length > 0) {
    context.value[0][0].ganttBarConfig.label = "修改后的计划 负责人：张三";
    context.value[0][0].ganttBarConfig.style = {
      background: "#f8bc45"
    };
  }
};

// 打印数据
const printData = () => {
  console.log(JSON.stringify(context.value, null, 2));
};