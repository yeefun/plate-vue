<template>
  <div class="embedded-iframe" />
</template>

<script>
import { get as _get } from 'lodash'

export default {
  name: 'EmbeddedIframe',
  props: {
    mediaData: {
      type: Object,
      default: () => ({}),
      required: true
    }
  },
  computed: {
    mediaDataEmbed () {
      return _get(this.mediaData, ['embed'], '')
    }
  },
  mounted () {
    const { top } = this.$el.getBoundingClientRect()
    const { height } = this.$store.state.viewport

    if (top <= height) {
      this.$el.innerHTML = this.mediaDataEmbed
    } else {
      window.addEventListener('scroll', this.loadIframe)
    }
  },
  methods: {
    loadIframe () {
      const { top } = this.$el.getBoundingClientRect()
      const { height } = this.$store.state.viewport

      if (top <= height) {
        this.$el.innerHTML = this.mediaDataEmbed
        window.removeEventListener('scroll', this.loadIframe)
      }
    }
  }
}
</script>

<style lang="stylus">
.embedded-iframe
  cursor pointer
  position relative
  & iframe
    display block
    margin-right auto
    margin-left auto
</style>
