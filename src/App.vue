<template>
  <div id="app">
    <input type="number" min="0" max="1" step="0.05" :value="value" @input="handleInputUpdateValue"/>
    <CircleProgressBar :value="value"/>
  </div>
</template>

<script>
import {defineComponent} from "vue";
import CircleProgressBar from "@/components/CircleProgressBar.vue";

export default defineComponent({
  components: {CircleProgressBar},
  data() {
    return {
      value: 0.5,
    }
  },
  methods: {
    handleInputUpdateValue(e) {
      const inputValue = e.target.value;
      this.value = parseFloat(inputValue) || 0
    }
  }
})
</script>

<style>
* {
  padding: 0;
  margin: 0;
}

#app {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 64px;

  height: 100dvh;

  background: #21163E;
}
</style>
