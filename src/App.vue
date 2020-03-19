<template>
  <div id="app">
    <h1>NovelTextBox</h1>
    <NovelTextBox
      content="loooooooooooooooooooooooooong string"
      :delay="delay" ref="textbox"/>
    <div class="control">
      <div class="input-wrapper">
        <h3>content</h3>
        <textarea class="text-input" placeholder="input text"
          @keyup="inputted" v-model="str"
        />
      </div>
      <div class="input-wrapper">
        <h3>delay</h3>
        <input class="text-input" type="number" v-model.number="delay">
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import NovelTextBox from './components/NovelTextBox.vue';

@Component({
  components: {
    NovelTextBox
  }
})

export default class App extends Vue {
  str = ''
  delay = 100

  inputted() {
    console.log('input');
  }
}
</script>

<style lang="scss">
body {
  box-sizing: border-box;
  width: 100%;
  margin: 0;
  padding: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.control {
  width: 80%;
  margin: 30px auto;
  display: flex;
  flex-direction: row;
}

.input-wrapper {
  width: 50%;
  margin: 0;
  padding: 0;
  text-align: left;
  h3 {
    margin: 0;
    margin-bottom: 8px;
  }
}

textarea {
  width: 80%;
}

.text-input {
  font-size: 18px;
  margin: 0;
  padding: 12px 8px;
  border: 1px solid #ccc;
  border-radius: 2px;
}
</style>
