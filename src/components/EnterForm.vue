<template>
  <form @submit.prevent>
    <input
        class="enter font"
        id="enterId"
        autocomplete="off"
        :value="inputEnter"
        @input="handleInput"
        @focus="startTyping"
        type="text"
        :style="{
          'background': checkingCorrect
        }"
    >
    <transition-group name="list-texts">
      <p
          class="enter-text font"
          v-for="text in texts"
          :key="text.id"
      >
        {{ text.content }}
      </p>
    </transition-group>
  </form>
</template>

<script>
export default {
  data() {
    return {
      inputEnter: "",
      lastInputLength: 0
    }
  },
  props: {
    texts: {
      type: Array,
      required: true
    }
  },
  computed: {
    checkingCorrect() {
      if (this.texts[0].content === this.inputEnter) {
        this.calculateSpeed();
        this.$emit("next")
        this.inputEnter = ""
        const enterId = document.getElementById("enterId")
        enterId.placeholder = "Предложение написано верно"
        return "white"
      } else if (this.texts[0].content.indexOf(this.inputEnter) === 0) {
        return "white"
      } else {
        return "red"
      }
    }
  },
  methods: {
    handleInput(event) {
      this.inputEnter = event.target.value;
      if (this.inputEnter.length === 1 && this.lastInputLength === 0) {
        this.$emit('start-typing');
      }
      this.lastInputLength = this.inputEnter.length;
    },
    startTyping() {
      this.$emit('start-typing');
    },
    calculateSpeed() {
      if (this.inputEnter.length > 0) {
        this.$emit('calculate-speed', this.inputEnter.length);
      }
    }
  }
}
</script>

<style scoped>
.enter {
  width: 100%;
  border: 2px solid deepskyblue;
  outline: none;
  border-radius: 5px;
  padding: 10px 13px;
}

.enter-text {
  margin: 15px;
}

.list-texts-move,
.list-texts-enter-active,
.list-texts-leave-active {
  transition: all 1s ease;
}

.list-texts-enter-from,
.list-texts-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

.list-texts-leave-active {
  position: absolute;
}

.font {
  font-size: 20px;
  font-family: "Arial";
}
</style>