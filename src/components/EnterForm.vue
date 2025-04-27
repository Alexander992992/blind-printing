<template>
  <form @submit.prevent>
    <input
        class="enter"
        id="enterId"
        :value="inputEnter"
        @input="inputEnter = $event.target.value"
        type="text"
        :style="{
          'background': checkingCorrect
        }"
    >
    <transition-group name="list-texts">
      <p
          class="enter-text"
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
      inputEnter: ""
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
  font-size: 20px;
  font-family: "Arial";
}

.enter-text {
  margin: 15px;
  font-size: 20px;
  font-family: "Arial";
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

</style>