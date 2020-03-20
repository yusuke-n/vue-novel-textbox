<template>
  <div id="app">
    <h1>vue-novel-textbox</h1>
    <div class="wrap msg">
      <div class="msg-window">
        <NovelTextBox
          class-name="fulltext-box"
          :content="contents"
          :ready="ready"
          :auto-start="false"
          :skip="skip"
          :delay="delay"
          :lf-symbol="lfSym"
          word-break="break-all"
          :key="k"/>
      </div>
    </div>
    <div class="control wrap">
      <div class="container">
        <div class="input-wrapper">
        <label>content</label>
        <div class="field">
          <textarea class="text-input" placeholder="input sentence(s)" v-model="str"/>
        </div>
      </div>
      <div class="input-wrapper">
        <label>delay</label>
        <div class="field">
          <input class="text-input" type="number" v-model.number="delay">
        </div>
      </div>
      <div class="input-wrapper">
        <label>skip</label>
        <div class="field">
          <div class="radio-item">
            <input class="checkbox-input" type="checkbox" v-model="skip">
            <label>skip mode</label>
            </div>
        </div>
      </div>
      <div class="input-wrapper">
        <label>lf-symbol</label>
        <div class="field">
          <input class="text-input" type="text" v-model="lfSym">
        </div>
      </div>
    </div>
    <div class="container start">
      <button @click="start">Start!!</button>
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
  str = 'The quick brown fox jumps over the lazy dog.'
  delay = 50
  ready = false
  skip = false
  k = 0
  lfSym = '<n>'

  mounted() {
    this.ready = true;
  }

  get contents() {
    return this.str.split('\n');
  }

  start() {
    this.ready = true;
    this.k += 1;
  }
}
</script>

<style lang="scss">
* {
  box-sizing: border-box;
}

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

.flex-horizontal {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.wrap {
  width: 80%;
  margin: 30px auto;
  display: flex;
  flex-direction: row;
}

.control {
  display: flex;
  flex-direction: row;
  .container {
    display: flex;
    flex-direction: column;
    flex-grow: 1;
    padding: 0;
    padding-right: 12px;
    &.start {
      display: flex;
      align-items: center;
      justify-content: center;
      flex-basis: 285px;
      button {
        padding: 14px 38px;
        border: 0;
        background: #87daa4;
        border-radius: 4px;
        font-size: 26px;
        color: #0a4e1c;
        cursor: pointer;
        &:hover,
        &:active {
          background: #afe6c2;
          color: #3b8e51;
        }
      }
    }
  }
}

.msg-window {
  width: 100%;
  height: 180px;
  text-align: left;
  background: #1b1d2d;
  border-radius: 5px;
  color: #fff;
  font-size: 24px;
  cursor: pointer;
  overflow-y: auto;
}

.input-wrapper {
  margin: 0;
  padding: 0;
  text-align: left;
  display: flex;
  flex-direction: row;
  align-items: center;
  margin-bottom: 12px;
  label {
    margin: 0;
    margin-right: 1rem;
    font-weight: bolder;
    width: 80px;
    flex-basis: 80px;
  }
  .field {
    flex-grow: 1;
  }
  .radio-item {
    margin-right: 1rem;
    label {
      font-weight: normal;
      width: 32px;
      margin-right: .5rem;
    }
  }
}

textarea {
  width: 420px;
  height: 212px;
}

.text-input {
  font-size: 16px;
  margin: 0;
  padding: 12px 8px;
  border: 1px solid #ccc;
  border-radius: 2px;
}

.fulltext-box {
  box-sizing: border-box;
  width: 100%;
  height: 100%;
  padding: 16px 32px
}

@media (max-width: 768px) {
  .control {
    flex-wrap: wrap-reverse;
    .container.start {
      margin-bottom: 24px;
    }
  }
  textarea {
    width: 100%;
    height: 142px;
  }
}

@media (max-width: 992px) {
  .input-wrapper {
    label {
      flex-basis: 60px;
      width: 60px;
    }
  }
  textarea {
    width: 100%;
  }
}
</style>
