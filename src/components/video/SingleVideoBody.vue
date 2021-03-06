<template>
  <section class="single-video">
    <slot name="PCHD" />
    <slot name="MBHD" />
    <div class="single-video__video">
      <OathPlayer
        :combined-id="combinedId"
        :player-id="playerId"
        :script-src="scriptSrc"
        :video-id="video.id"
      />
      <h1 v-text="video.name" />
      <div class="single-video__video-info">
        <p
          class="small"
          v-text="moment(video.publishDate).format('YYYY. MM. DD')"
        />
        <slot name="share" />
      </div>
      <p v-text="video.description" />
    </div>
    <template v-if="latest.length > 0">
      <div class="single-video__latest">
        <slot name="PCR1" />
        <slot name="MBE1" />
        <h3>最新影音</h3>
        <a
          v-for="item in latest"
          :key="item.id"
          :href="`/video/${item.id}`"
          class="latest"
          target="_blank"
          @click="sendGaClickEvent('article', 'video latest')"
        >
          <figure>
            <img
              :src="getThumbnails(item)"
              :alt="item.name"
            >
          </figure>
          <p v-text="item.name" />
        </a>
      </div>
    </template>

    <slot name="PCFT" />
    <slot name="MBFT" />
  </section>
</template>
<script>

import OathPlayer from './OathPlayer.vue'
import moment from 'moment'
import { OATH_COPMANY_ID, OATH_PLAYER_LIST, SITE_URL } from '../../constants'

import { sendGaClickEvent } from '../../util/comm'

export default {
  name: 'SingleVideoBody',
  components: {
    OathPlayer
  },
  props: {
    video: {
      type: Object,
      default: () => ({})
    },
    videos: {
      type: Array,
      default: () => []
    }
  },
  data () {
    return {
      SITE_URL,
      mounted: false
    }
  },
  computed: {
    combinedId () {
      return `${this.playerId}${OATH_COPMANY_ID}`
    },
    latest () {
      return this.videos.filter(video => video.id !== this.video.id).slice(0, 7)
    },
    playerId () {
      const type = this.$store.state.viewport.width > 768 ? 'desktop' : 'mobile'
      return OATH_PLAYER_LIST.singleVideo[type]
    },
    scriptSrc () {
      const videoId = this.$route.fullPath.split('/')[2]
      return `//delivery.vidible.tv/jsonp/pid=${this.playerId}/vid=${videoId}/${OATH_COPMANY_ID}.js`
    }
  },
  mounted () {
    this.mounted = true
  },
  methods: {
    getThumbnails (video) {
      return video.systemThumbnails.length > 0 ? video.systemThumbnails[video.systemThumbnails.length - 1].url : ''
    },
    moment,
    sendGaClickEvent
  }
}
</script>
<style lang="stylus" scoped>
.single-video
  &__video
    > p
      width 90%
      margin 0 auto
    h1
      width 90%
      margin 1em auto 0
      color #064f77
      text-align justify
      font-size 1.1875rem
      line-height 1.37
    p
      margin-top .5em
      text-align justify
      line-height 1.88
      &.small
        color #a0a0a0
        font-size .75rem
  &__video-info
    display flex
    justify-content space-between
    width 90%
    margin 15px auto 0
    > p
      display inline-block
      margin 0
  &__latest
    width 100%
    margin 40px auto 0
    h3
      width 90%
      margin 0 auto
      color #2a597d
      font-size 1rem
      font-weight 600
    .latest
      display block
      width 90%
      margin 20px auto 0
      figure
        position relative
        padding-top 56.25%
        margin 0
        overflow hidden
        img
          position absolute
          top 0
          left 0
          right 0
          bottom 0
          width 100%
          height 100%
          object-fit cover
          object-position center center
      p
        margin 10px 0 0
        color #828282
        font-size .875rem
        text-align justify
        line-height 1.43

@media (min-width: 768px)
  .single-video
    &__video
      > h1, > p
        width 60%
      h1
        font-size 1.375rem
    &__video-info, &__latest
      width 60%
    &__latest
      h3
        width auto
      .latest
        display flex
        width auto
        figure
          width 50%
          padding-top 28.125%
          margin 0 10px 0 0
        p
          flex 1
          font-size 1rem

@media (min-width: 1200px)
  .single-video
    display flex
    flex-wrap wrap
    max-width 1160px
    margin 30px auto 0
    &__video
      width 66.67%
      > h1, > p
        width 100%
      h1
        margin-right 2em
      p.small
        font-size .875rem
    &__video-info
      width 100%
    &__latest
      width calc(33.33% - 50px)
      margin 0 0 0 50px
      h3
        font-size 1.25rem
      .latest
        figure
          width 40%
          padding-top 22.5%
        p
          margin 0
          font-size .875rem
          &:hover
            color #064f77
</style>
