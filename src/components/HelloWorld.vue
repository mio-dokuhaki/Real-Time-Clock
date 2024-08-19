<template>
  <div class="clock">
    <div class="clock-face">
      <div class="dot" v-for="n in 60" :key="n" :style="dotStyle(n)"></div>
      <!-- ドットを先に配置 -->
      <div class="number" v-for="n in 12" :key="n" :style="numberStyle(n)">
        {{ n === 12 ? n : n }}
        <!-- 12を表示 -->
      </div>
      <div class="hand hour" :style="hourStyle"></div>
      <div class="hand minute" :style="minuteStyle"></div>
      <div class="hand second" :style="secondStyle"></div>
      <div class="center-dot"></div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from "vue";

const now = ref(new Date());

const updateTime = () => {
  const utcNow = new Date();
  const jstNow = new Date(
    utcNow.getTime() + utcNow.getTimezoneOffset() * 60000 + 9 * 3600000,
  );
  now.value = jstNow;
};

const getRotationStyle = (degrees: number) => {
  return {
    transform: `rotate(${degrees - 90}deg)`, // 90度反時計回りに回転
  };
};

const dotStyle = (n: number) => {
  const angle = n * 6; // 360度 / 60 = 6度ごとにドットを配置
  const radius = 48; // ドットを時計の円周のギリギリ内側に配置
  return {
    position: "absolute",
    left: `calc(50% + ${radius * Math.cos((angle * Math.PI) / 180)}%)`,
    top: `calc(50% + ${radius * Math.sin((angle * Math.PI) / 180)}%)`,
    transform: "translate(-50%, -50%)",
    width: n % 5 === 0 ? "1vw" : "0.5vw", // 5分刻みのドットを大きくする
    height: n % 5 === 0 ? "1vw" : "0.5vw",
    backgroundColor: "currentColor",
    borderRadius: "50%",
  };
};

const numberStyle = (n: number) => {
  const angle = (n - 3) * 30; // 3を0度の位置にして30度ずつ回転
  const radius = 40; // 数字をドットより内側に配置
  return {
    position: "absolute",
    left: `calc(50% + ${radius * Math.cos((angle * Math.PI) / 180)}%)`,
    top: `calc(50% + ${radius * Math.sin((angle * Math.PI) / 180)}%)`,
    transform: "translate(-50%, -50%)",
    fontSize: "2vw",
    color: "currentColor", // 色をカスタマイズする
  };
};

const hourStyle = ref({});
const minuteStyle = ref({});
const secondStyle = ref({});

onMounted(() => {
  updateTime();
  setInterval(() => {
    updateTime();

    const hours = now.value.getHours();
    const minutes = now.value.getMinutes();
    const seconds = now.value.getSeconds();
    const milliseconds = now.value.getMilliseconds();

    const hourDegrees = (hours % 12) * 30 + minutes * 0.5;
    const minuteDegrees = minutes * 6 + seconds * 0.1;
    const secondDegrees = seconds * 6 + milliseconds * 0.006;

    hourStyle.value = getRotationStyle(hourDegrees);
    minuteStyle.value = getRotationStyle(minuteDegrees);
    secondStyle.value = getRotationStyle(secondDegrees);
  }, 16);
});
</script>

<style scoped>
.clock {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f0f8ff;
}

.clock-face {
  position: relative;
  width: 40vw;
  height: 40vw;
  max-width: 80vh;
  max-height: 80vh;
  border: 1vw solid #61dafb;
  border-radius: 50%;
  background-color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0px 0px 15px rgba(0, 0, 0, 0.1);
}

.number {
  position: absolute;
  transform: translate(-50%, -50%);
  font-size: 2vw;
  color: black; /* 色をカスタマイズする */
}

.dot {
  position: absolute;
  background-color: #333; /* ドットの色を設定 */
  border-radius: 50%;
}

.hand {
  position: absolute;
  width: 50%;
  height: 2px;
  background-color: black;
  top: 50%;
  left: 50%;
  transform-origin: 0% 50%;
  transform: translate(-50%, -50%);
  transition: transform 0.05s ease-in-out;
}

.hour {
  width: 30%;
  height: 4px;
  background-color: #333;
}

.minute {
  width: 40%;
  height: 3px;
  background-color: #666;
}

.second {
  width: 45%;
  height: 2px;
  background-color: #ff6347;
}

.center-dot {
  position: absolute;
  width: 2vw;
  height: 2vw;
  background-color: #000;
  border-radius: 50%;
  z-index: 10;
}
</style>
