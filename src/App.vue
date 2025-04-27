<template>
  <div class="app">
    <div class="langSelect">
      <select v-model="nowLang" @change="langChange">
        <option v-for="lang in langs">
          {{ lang }}
        </option>
      </select>
    </div>
    <enter-form
        v-if="texts.length > 0"
        :texts="texts.slice(nowText,nowText+4)"
        @next="nextText"
    />
    <div v-else>Загрузка текстов...</div>
  </div>
</template>

<script>
import EnterForm from "@/components/EnterForm";
import axios from "axios";

export default {
  components: {
    EnterForm
  },
  data() {
    return {
      texts: [],
      nowText: 0,
      nowLang: "ru",
      langs: ["ru", "en"]
    }
  },
  methods: {
    langChange() {
      this.nowText = 0;
      this.fetchTexts();
    },
    nextText(){
      if (this.nowText < this.texts.length - 1){
        this.nowText += 1;
      } else {
        this.nowText = 0;
        this.fetchTexts();
      }
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
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.langSelect {
  position: absolute;
  top: 20px;
  right: 20px;
}

.langSelect select {
  padding: 8px 12px;
  border: 2px solid deepskyblue;
  border-radius: 5px;
  background-color: white;
  font-size: 14px;
  outline: none;
}

</style>