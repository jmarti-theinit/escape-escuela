<template>
  <div>
    <div class="zoomed-item box" @click.stop="">
      <span class="helper"></span>
      <img id="fieldImage" alt="pizarra hint" :src="getUrl()" @load="recalculateClientRect()" />
      <div :style="textStyle" class="theHintText">
        <span v-for="(ltr,idx) in letters" :key="idx">
          <span v-if="isOrdinaryLetter(ltr)">{{ ltr }}</span>
          <img v-else :style="imgCharStyle" class="evilChar" :src="getLetterSrc(ltr)" :alt="ltr" />
        </span>
      </div>
    </div>
  </div>
</template>
<style scoped lang="scss">
.zoomed-item {
  position: fixed;
  top: 10vh;
  left: 10vw;
  height: 80vh;
  width: 80vw;
  background: black;
  white-space: nowrap; /* This is required unless you put the helper span closely near the img */
  text-align: center;
  border-color: $primary-color;
  border-width: 2vh;
}

.zoomed-item img {
  max-height: 80vh;
  max-width: 80vw;
  width: auto;
  vertical-align: middle;
}

.helper {
  display: inline-block;
  height: 100%;
  vertical-align: middle;
}

.theHintText {
  position: fixed;
  color: rgba(255, 255, 255, 0.8);
  font-family: 'PrimaryFontFamily',serif;
}

.evilChar {
  border-bottom-color:#fff;
  border-bottom-width:0.5vh;
  border-bottom-style: dotted;
}
</style>
<script>
export default {
  name: 'PizarraHint',
  props: {
    image: {
      type: String,
      default: '',
    },
    top: {
      type: Number,
      default: 0,
    },
    left: {
      type: Number,
      default: 0,
    },
    text: {
      type: String,
      default: '',
    },
    fontSize: {
      type: Number,
      default: 4,
    },
    evilName: {
      type: String,
      default: '',
    },
    evilLetters: {
      type: Object,
      default: () => {},
    }
  },
  data() {
    return {
      publicPath: process.env.BASE_URL,
      fieldImageClientRect: null,
    }
  },
  computed: {
    textStyle() {
      return {
        top: this.fieldImageClientRect ?
            Math.round((this.fieldImageClientRect.top + (this.fieldImageClientRect.height * this.top / 100) )) + 'px' :
            '0px',
        left: this.fieldImageClientRect ?
            Math.round((this.fieldImageClientRect.left + (this.fieldImageClientRect.width * this.left / 100) )) + 'px' :
            '0px',
        fontSize: this.fieldImageClientRect ?
            Math.round(this.fieldImageClientRect.width * this.fontSize / 100) + 'px' :
            '12px',
      };
    },
    imgCharStyle() {
      return {
        width: this.fieldImageClientRect ?
            Math.round(this.fieldImageClientRect.width * this.fontSize * 0.75 / 100) + 'px' :
            '12px',
      };
    },
    letters() {
      return this.text.split('');
    },
  },
  created() {
    window.addEventListener("resize", this.recalculateClientRect);
  },
  destroyed() {
    window.removeEventListener("resize", this.recalculateClientRect);
  },
  methods: {
    getUrl() {
      return this.publicPath + this.image;
    },
    recalculateClientRect() {
      this.fieldImageClientRect = document.getElementById('fieldImage') ?
          document.getElementById('fieldImage').getBoundingClientRect() : null;
    },
    isOrdinaryLetter(ltr) {
      return ltr===' ' || this.evilName.toUpperCase().indexOf(ltr.toUpperCase()) === -1;
    },
    getLetterSrc(ltr) {
      const letterFile = this.evilLetters[ltr.toUpperCase()];
      return `${this.publicPath}game/evil-letters/letra-${letterFile}.png`
    },
  },
};
</script>
