<template>
  <div class="app">
    <div class="lefttop">
      <div class="speedText font">
        <div>Скорость печати: {{ speedNow }} зн/мин</div>
        <div>Средняя скорость печати: {{ averageSpeed }} зн/мин</div>
        <my-button @click="resetSpeedData">Сбросить</my-button>
      </div>
    </div>
    <div class="main">
      <enter-form
          v-if="texts.length > 0"
          :texts="texts.slice(nowText,nowText+4)"
          @next="nextText"
          @start-typing="startTypingTimer"
          @calculate-speed="calculateSpeed"
      />
      <div class="font" v-else>Загрузка текстов...</div>
    </div>
    <div class="righttop">
      <my-select
          :options="levels"
          :option="nowLevel"
          v-model="nowLevel"
          @change="levelChange"
      />
      <my-select
          :options="langs"
          :option="nowLang"
          v-model="nowLang"
          @change="langChange"
      />
    </div>
  </div>
</template>

<script>
import EnterForm from "@/components/EnterForm";
import MyButton from "@/components/MyButton";
import MySelect from "@/components/MySelect";
import axios from "axios";

export default {
  components: {
    EnterForm,
    MyButton,
    MySelect,
  },
  data() {
    return {
      texts: [],
      nowText: 0,
      nowLang: "ru",
      langs: [
        {id: "ru", value: "Русский"},
        {id: "en", value: "English"}
      ],
      nowLevel: 1,
      levels: [
        {id: 1, value: "Начальный"},
        {id: 2, value: "Продвинутый"},
        {id: 3, value: "Профессиональный"}
      ],
      speedNow: 0,
      averageSpeed: 0,
      speedHistory: [],
      typingStartTime: null
    }
  },
  methods: {
    langChange() {
      this.nowText = 0;
      this.fetchTexts();
      this.resetSpeedData();
    },
    levelChange() {
      this.nowText = 0;
      this.fetchTexts();
      this.resetSpeedData();
    },
    nextText(){
      if (this.nowText < this.texts.length - 1){
        this.nowText += 1;
      } else {
        this.nowText = 0;
      }
    },
    resetSpeedData() {
      this.speedNow = 0;
      this.averageSpeed = 0;
      this.speedHistory = [];
    },
    startTypingTimer() {
      this.typingStartTime = new Date();
    },
    calculateSpeed(textLength) {
      if (!this.typingStartTime) return;
      const endTime = new Date();
      const timeTaken = (endTime - this.typingStartTime) / 1000;
      if (textLength > 0 && timeTaken > 0) {
        const speed = Math.round((textLength / timeTaken) * 60);
        this.speedNow = speed;
        this.speedHistory.push(speed);
        const sum = this.speedHistory.reduce((a, b) => a + b, 0);
        this.averageSpeed = Math.round(sum / this.speedHistory.length);
      }
      this.typingStartTime = null;
    },
    async fetchTexts() {
      try {
        const response = await axios.get(`https://blind-printing-tab-default-rtdb.europe-west1.firebasedatabase.app/${this.nowLang}/${this.nowLevel}.json`);
        this.texts = response.data;
      } catch (e) {
        alert("Ошибка")
      }
    }
  },
  mounted() {
    this.fetchTexts()
  }
}
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.app {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 2fr;
  gap: 10px 10px;
  height: 100vh;
  align-items: start;
}

.speedText {
  border: 2px solid deepskyblue;
  border-radius: 5px;
  padding: 10px;
}

.lefttop {
  justify-self: start;
  grid-column: 1 / 3;
  grid-row: 1 / 2;
}

.main {
  justify-self: center;
  align-self: start;
  grid-column: 1 / 6;
  grid-row: 2 / 3;
}

.righttop {
  display: flex;
  justify-self: end;
  grid-column: 5 / 6;
  grid-row: 1 / 2;
}


.font {
  margin: 10px;
  font-size: 20px;
  font-family: "Arial";
}
</style>