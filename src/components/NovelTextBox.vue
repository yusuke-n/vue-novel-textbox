<template>
<div class="novel-textbox-wrapper" @click="onClick">
  <span>{{ displayed }}</span>
</div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

const NovelTextProp = Vue.extend({
  props: {
    content: String,
    delay: Number
  }
});

@Component
export default class NovelTextBox extends NovelTextProp {
  currentIdx = 0;
  displayed = '';
  itr: Generator | null = null;
  skipped = false;

  created() {
    this.itr = this.genIterator();
    this.displayAsync();
  }

  * genIterator() {
    for (let i = 0; i < this.content.length; i++) {
      yield this.content[i];
      this.currentIdx = i;
    }
  }

  async displayAsync() {
    for (let i = 0; i < this.content.length; i++) {
      if (i > 0) {
        await this.sleep(this.delay);
      }
      if (this.skipped) {
        break;
      }

      if (this.itr !== null) {
        this.displayed += this.itr.next().value;
      } else {
        throw new Error();
      }
    }
  }

  displayAll() {
    this.displayed = this.content.slice(0);
  }

  onClick() {
    this.skipped = true;
    this.displayAll();
  }

  reset() {
    this.displayed = '';
    this.currentIdx = 0;
    this.skipped = false;
  }

  sleep(delay: number) {
    return new Promise((resolve, reject) => {
      setTimeout(() => { resolve(); }, delay);
    });
  }
}
</script>

<style lang="scss" scoped>
.novel-textbox-wrapper {
    margin: 0
}
</style>
