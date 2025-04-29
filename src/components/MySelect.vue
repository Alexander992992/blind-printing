<template>
  <select class="font" :value="modelValue" @change="langChange">
    <option v-for="option in options" :key="option" :value="option">
      {{ option }}
    </option>
  </select>
</template>

<script>
export default {
  props: {
    options: {
      type: Array,
      required: true
    },
    modelValue: {
      type: String,
      required: true
    }
  },
  methods: {
    langChange(event) {
      this.$emit("update:modelValue", event.target.value)
    }
  }
}
</script>

<style scoped>

select {
  padding: 5px 12px;
  width: 60px;
  height: 40px;
  border: 2px solid deepskyblue;
  border-radius: 5px;
  background-color: white;
  outline: none;
}

.font {
  margin: 10px;
  font-size: 20px;
  font-family: "Arial";
}

</style>