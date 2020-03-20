<template>
<div class="novel-textbox-wrapper"
  :class="className"
  @pointerdown="onClick"
>
  <span
    :class="{ 'supress-select': supressSelect }"
    :style="{ 'word-break': wordBreakMode }"
    v-for="(s, k) in displayed"
    :key="k"
  >
    {{ s }}
  </span>
  <span
    :class="{ 'supress-select': supressSelect }"
    :style="{ 'word-break': wordBreakMode }"
  >
    {{ currentTyping }}
  </span>
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
  @Prop({ default: 'word-break' }) readonly wordBreak!: string;
  @Prop({ default: '\n' }) readonly lfSymbol!: string;
  @Watch('ready')
  onReadyChanged(val: boolean, oldVal: boolean) {
    if (!oldVal && val) {
      this.reset();
      this.displayWithDelay();
    }
  }

  currentStringIdx = 0;
  currentSentenceIdx = 0;
  displayed: string[] = [];
  currentTyping = '';
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

  get normalizedContent(): string[] {
    return this.content
      .filter((s) => s !== '')
      .map((s) => s.replace(this.lfSymbol, '\n'));
  }

  get sentence(): string {
    const s = this.normalizedContent[this.currentSentenceIdx];
    return s === undefined ? '' : s;
  }

  get wordBreakMode(): string {
    switch (this.wordBreak) {
      case 'break-all':
      case 'keep-all' :
      case 'break-word':
        return this.wordBreak;
      default:
        return 'normal';
    }
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
      if (itrRes.value === '\n') {
        this.lineFeed();
      }
      this.currentTyping += itrRes.value;
      itrRes = this.itr.next();
    }
    this.sentenceFinished = true;
    if (this.currentSentenceIdx === this.normalizedContent.length - 1) {
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
    this.currentTyping = '';
    this.displayed = this.sentence.split('\n');
  }

  lineFeed() {
    this.displayed.push(this.currentTyping);
    this.currentTyping = '';
  }

  onClick() {
    if (this.sentenceFinished) {
      this.nextSentence();
    } else {
      this.skipDisplay();
    }
  }

  reset() {
    this.currentTyping = '';
    this.currentStringIdx = 0;
    this.currentSentenceIdx = 0;
    this.skipped = false;
  }

  nextSentence() {
    if (this.currentSentenceIdx >= this.content.length - 1) {
      return;
    }
    this.displayed = [];
    this.currentTyping = '';
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
  display: block;
  width: 100%;
  overflow-wrap: break-word;
  &.supress-select {
    user-select: none;
  }
}
</style>
