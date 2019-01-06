<template>
  <div class="sliding-title">
    <div>
      <div class="title" :style="titleStyle">{{ title }}</div>
    </div>
    
    <div class="separator"/>

    <div class="content">
      <slot/>
    </div>
  </div>
</template>

<script>
import { eventBus } from 'src/js/event-bus.js';

export default {
  props: {
    scroll: { type: Number, required: true },
    title: { type: String, required: true },
  },
  data() {
    return {
      top: 0,
      height: 0,
      titleHeight: 0,
      // using the following check to make animations smoother on mobile devices (unnecessary/makes it worse on desktops)
      isMobileDevice: window.navigator.userAgent.toLowerCase().includes("mobi"),
    }
  },
  mounted() {
    this.setProperties();

    // when the height of some other element changes, the offsetTop of this element will also change
    eventBus.$on('height-change', () => this.setProperties());
  },
  computed: {
    titleTranslateY() {
      if (this.scroll <= this.top) {
        return 0;
      } else if (this.scroll <= this.top + (this.height - this.titleHeight)) {
        return this.scroll - this.top;
      } else {
        return this.height - this.titleHeight;
      }
    },
    titleStyle() {
      const style = {
        transform: `translateY(${this.titleTranslateY}px)`,
      }
      if (this.isMobileDevice) {
        style.transition = 'transform .1s';
      }
      return style;
    }
  },
  methods: {
    setProperties() {
      this.top = this.$el.offsetTop;
      this.height = this.$el.offsetHeight;
      this.titleHeight = this.$el.querySelector('.title').offsetHeight;
    }
  }
}
</script>

<style lang="sass" scoped>
@import 'src/style.sass'

.sliding-title
  // height: 100%
  // overflow: auto
  // padding-top: $menu-height
  // position: relative
  margin: auto
  max-width: 1000px
  // border-radius: 20px
  // background-color: lighten($primary-color, 60%)
  display: flex
  margin-top: 50px

  .title
    font-size: 2.25em
    font-weight: bold
    // margin-top: 30px
    // margin-left: 10px
    +shadow
    text-align: center
    width: 225px
    line-height: 75px
    display: inline-block
    // padding: 15px 40px
    border-radius: 10px
    border-bottom: $primary-color 4px solid
    // box-sizing: border-box
    margin: 0 25px
    // margin-top: 10px
    color: rgba(0, 0, 0, .8)

  .separator
    // height: 100%
    width: 3px
    background-color: $primary-color
    margin: 0 15px
    // flex-basis: 20px
    border-radius: 5px

  .content
    margin: 0 15px
    flex: 1

</style>