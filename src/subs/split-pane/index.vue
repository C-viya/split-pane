<template>
  <div
    :style="{cursor, userSelect}"
    class="vue-splitter-container clearfix"
    @mouseup="onMouseUp"
    @mousemove="onMouseMove"
  >
    <pane
      v-if="show"
      class="splitter-pane splitter-paneL"
      :split="split"
      :style="{ [type]: percent+'%'}"
    >
      <slot name="paneL" />
    </pane>

    <div class="split-line">
      <resizer
        :class-name="className"
        :style="{ [resizeType]: percent+'%'}"
        :split="split"
        @mousedown.native="onMouseDown"
        @click.native="onClick"
        @closeLeft="closeLeft"
        @openLeft="show=true"
      />
      <i v-if="show" class="iconfont iconjiantou_zuo close" :style="{ [resizeType]: `${containerWidth*percent/100 - 25}` +'px'}" @click="closeLeft()" />
      <i v-if="!show" class="iconfont iconjiantou_you open" :style="{ [resizeType]: `${containerWidth*percent/100 + 5}` +'px'}" @click="openLeft()" />
    </div>

    <pane
      class="splitter-pane splitter-paneR"
      :split="split"
      :style="{ [type]: 100-percent+'%'}"
    >
      <slot name="paneR" />
    </pane>
    <div
      v-if="active"
      class="vue-splitter-container-mask"
    />
  </div>
</template>

<script>
import Resizer from './resizer.vue';
import Pane from './pane.vue';

export default {
  name: 'SplitPane',
  components: { Resizer, Pane },
  props: {
    minPercent: {
      type: Number,
      default: 10
    },
    defaultPercent: {
      type: Number,
      default: 50
    },
    split: {
      validator(value) {
        return ['vertical', 'horizontal'].indexOf(value) >= 0;
      },
      required: true
    },
    className: {
      type: String,
      default: ''
    },
    containerWidth: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      active: false,
      hasMoved: false,
      height: null,
      percent: this.defaultPercent,
      type: this.split === 'vertical' ? 'width' : 'height',
      resizeType: this.split === 'vertical' ? 'left' : 'top',
      show: true
    };
  },
  computed: {
    userSelect() {
      return this.active ? 'none' : '';
    },
    cursor() {
      return this.active ? (this.split === 'vertical' ? 'col-resize' : 'row-resize') : '';
    }
  },
  watch: {
    defaultPercent(newValue) {
      this.percent = newValue;
    }
  },
  methods: {
    onClick() {
      if (!this.hasMoved) {
        this.percent = 50;
        this.$emit('resize', this.percent);
      }
    },
    onMouseDown() {
      this.active = true;
      this.hasMoved = false;
    },
    onMouseUp() {
      this.active = false;
    },
    onMouseMove(e) {
      if (e.buttons === 0 || e.which === 0) {
        this.active = false;
      }

      if (this.active) {
        let offset = 0;
        let target = e.currentTarget;
        if (this.split === 'vertical') {
          while (target) {
            offset += target.offsetLeft;
            target = target.offsetParent;
          }
        } else {
          while (target) {
            offset += target.offsetTop;
            target = target.offsetParent;
          }
        }

        const currentPage = this.split === 'vertical' ? e.pageX : e.pageY;
        const targetOffset = this.split === 'vertical' ? e.currentTarget.offsetWidth : e.currentTarget.offsetHeight;
        const percent = Math.floor(((currentPage - offset) / targetOffset) * 10000) / 100;

        if (percent > this.minPercent && percent < 100 - this.minPercent) {
          this.percent = percent;
        }

        this.$emit('resize', this.percent);
        this.hasMoved = true;
      }
    },
    closeLeft() {
      this.show = false;
      this.percent = 0;
    },
    openLeft() {
      this.show = true;
      this.percent = this.defaultPercent;
    }
  }
};
</script>

<style lang="scss" scoped>
.clearfix:after {
  visibility: hidden;
  display: block;
  font-size: 0;
  content: " ";
  clear: both;
  height: 0;
}

.vue-splitter-container {
  height: 100%;
  position: relative;
}

.vue-splitter-container-mask {
  z-index: 9999;
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}
.split-line {
  .close,
  .open {
    position: absolute;
    top: 50%;
    width: 20px;
    height: 20px;
    color: #8b8b8b;
    cursor: pointer;
    z-index: 1;
  }
  .close {
    right: 0;
  }
}
.v-icon.v-icon::after {
  background-color: #fff !important;
}
</style>
