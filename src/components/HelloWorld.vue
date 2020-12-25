<template>
  <div ref="container" class="hello">
    <split-pane :default-percent="30" :min-percent="25" :container-width="containerWidth" split="vertical">
      <template slot="paneL">
        <div class="left-container">
          LEFT
        </div>
      </template>
      <template slot="paneR">
        <div class="right-container">
          RIGHT
        </div>
      </template>
    </split-pane>
  </div>
</template>

<script>
import splitPane from '@/subs/split-pane';
export default {
  name: 'HelloWorld',
  components: { splitPane },
  props: {
    msg: String
  },
  data() {
    return {
      containerWidth: 0
    }
  },
  mounted() {
    this.containerWidth = this.$refs.container.clientWidth;
    window.addEventListener('resize', () => {
      this.containerWidth = this.$refs.container.clientWidth;
    });
  },
}
</script>

<style lang="scss" scoped>
.hello {
  height: 500px;
  .left-container, .right-container {
    background-color: white;
    height: 100%;
    overflow: auto;
  }
}
</style>
