<template>
  <div class="app">
    <div class="head">
      <div class="speedText font">
        <div>Скорость печати: {{ speedNow }} зн/мин</div>
        <div>Средняя скорость печати: {{ averageSpeed }} зн/мин</div>
        <my-button @click="resetSpeedData">Сбросить</my-button>
      </div>
      <select class="langSelect font" v-model="nowLang" @change="langChange">
        <option v-for="lang in langs">
          {{ lang }}
        </option>
      </select>
    </div>
    <div class="main">
      <enter-form
          v-if="texts.length > 0"
          :texts="texts.slice(nowText,nowText+4)"
          @next="nextText"
          @start-typing="startTypingTimer"
          @calculate-speed="calculateSpeed"
      />
      <div v-else>Загрузка текстов...</div>
    </div>
    <div class="footer">
      <h3>Автор</h3>
    </div>
  </div>
</template>

<script>
import EnterForm from "@/components/EnterForm";
import MyButton from "@/components/MyButton";
import axios from "axios";

export default {
  components: {
    EnterForm, MyButton
  },
  data() {
    return {
      texts: [],
      nowText: 0,
      nowLang: "ru",
      langs: ["ru", "en"],
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
        const response = await axios.get(`https://blind-printing-tab-default-rtdb.europe-west1.firebasedatabase.app/${this.nowLang}.json`);
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
  grid-template-columns: 1fr;
  grid-template-rows: 150px 1fr 100px;
  gap: 10px 10px;
  height: 100vh;
}

.langSelect {
  padding: 5px 12px;
  width: 60px;
  height: 50px;
  border: 2px solid deepskyblue;
  border-radius: 5px;
  background-color: white;
  outline: none;
}

.speedText {
  border: 2px solid deepskyblue;
  border-radius: 5px;
  padding: 10px;
}

.head {
  display: flex;
  justify-self: end;
  align-self: center;
}

.main {
  justify-self: center;
  align-self: center;
}

.font {
  margin: 10px;
  font-size: 20px;
  font-family: "Arial";
}
</style>