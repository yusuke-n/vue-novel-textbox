<template>
<div class="novel-textbox-wrapper"
  :class="className"
  @pointerdown="onClick"
>
  <span :class="{ 'supress-select': supressSelect }">{{ displayed }}</span>
</div>
</template>

<script lang="ts">
import { Component, Vue, Prop, Watch } from 'vue-property-decorator';

@Component
export default class NovelTextBox extends Vue {
  @Prop({ default: '' }) readonly content!: string[];
  @Prop({ default: 100 }) readonly delay!: number;
  @Prop({ default: false }) readonly ready!: boolean;
  @Prop() readonly className?: string | string[];
  @Prop({ default: false }) readonly skip!: boolean;
  @Prop({ default: false }) readonly autoStart!: boolean;
  @Prop({ default: true }) readonly supressSelect!: boolean;
  @Watch('ready')
  onReadyChanged(val: boolean, oldVal: boolean) {
    if (!oldVal && val) {
      this.reset();
      this.displayWithDelay();
    }
  }

  currentStringIdx = 0;
  currentSentenceIdx = 0;
  displayed = '';
  itr: Generator | null = null;
  skipped = false;
  sentenceFinished = false;
  started = false;

  created() {
    this.itr = this.genIterator();
  }

  mounted() {
    if (this.autoStart || this.ready) {
      this.start();
    }
  }

  * genIterator() {
    for (let i = 0; i < this.sentence.length; i++) {
      yield this.sentence[i];
      this.currentStringIdx = i;
    }
  }

  get trimmedContent(): string[] {
    return this.content.filter((s) => s !== '');
  }

  get sentence(): string {
    const s = this.trimmedContent[this.currentSentenceIdx];
    return s === undefined ? '' : s;
  }

  start() {
    if (this.skip) {
      this.displayWithDelay(0);
    } else {
      this.displayWithDelay();
    }
  }

  async displayWithDelay(delay?: number) {
    if (this.itr === null) {
      throw new Error();
    }

    let itrRes = this.itr.next();
    while (!itrRes.done) {
      if (this.currentStringIdx > 0) {
        const d = delay === undefined ? this.delay : delay;
        await this.wait(d);
      }
      if (this.skipped) {
        break;
      }
      this.displayed += itrRes.value;
      itrRes = this.itr.next();
    }
    this.sentenceFinished = true;
    if (this.currentSentenceIdx === this.trimmedContent.length - 1) {
      this.$emit('paragraph-end');
    } else {
      this.$emit('sentence-end', this.currentSentenceIdx);
    }
    if (this.skip) {
      this.nextSentence();
    }
  }

  skipDisplay() {
    this.skipped = true;
    this.displayed = this.sentence.slice(0);
  }

  onClick() {
    if (this.sentenceFinished) {
      this.nextSentence();
    } else {
      this.skipDisplay();
    }
  }

  reset() {
    this.displayed = '';
    this.currentStringIdx = 0;
    this.currentSentenceIdx = 0;
    this.skipped = false;
  }

  nextSentence() {
    if (this.currentSentenceIdx >= this.content.length - 1) {
      return;
    }
    this.displayed = '';
    this.currentStringIdx = 0;
    this.currentSentenceIdx += 1;
    this.skipped = false;
    this.sentenceFinished = false;
    this.itr = this.genIterator();
    this.start();
  }

  wait(delay: number) {
    return new Promise((resolve) => {
      setTimeout(() => { resolve(); }, delay);
    });
  }
}
</script>

<style lang="scss" scoped>
span {
  width: 100%;
  overflow-wrap: break-word;
  &.supress-select {
    user-select: none;
  }
}
</style>
