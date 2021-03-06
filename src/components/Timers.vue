<template>
  <div id="home">
    <div id="date">{{ date }}</div>
    <div id="tips">
      {{ !active.category ? 'tap a timer to start logging time' : 'tap again to stop timer' }}
    </div>
    <div id="tracking">
      <div v-for="category in categories"
        :key="category"
        :class="{
          active: active.category === category,
          inactive: active.category && active.category !== category
        }">
        <div class="circle"
          @click="tapTimer(category)"
          :style="{
            borderColor: colors[category],
            backgroundColor: active.category === category ? colors[category] : '#fff'
          }">
          {{ timers[category] + ((active.category === category) ? currentTime : 0) | time }}
        </div>
        <div class="cat">
          {{ category }}
        </div>
      </div>
    </div>
    <div class="row center">
      <div id="cancel"
        v-show="active.category"
        @click="cancelTimer"
        :class="{ confirm }">
        {{ !confirm ? 'cancel timer' : 'tap again to confirm' }}
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import auth from '@/auth'

export default {
  data() {
    return {
      auth,
      confirm: false,
      date: this.getDate(),
      currentTime: 0,
      updateInterval: 0,
      cancelTimeout: 0
    }
  },

  computed: mapGetters(['active', 'categories', 'colors', 'timers']),

  methods: {
    ...mapActions(['cancelActive', 'toggleTimer', 'checkDayReset']),
    cancelTimer() {
      clearTimeout(this.cancelTimeout)
      if (!this.confirm) {
        this.confirm = true
        this.cancelTimeout = setTimeout(() => (this.confirm = false), 3000)
      } else {
        this.cancelActive()
        this.confirm = false
      }
    },
    getDate() {
      // returns date formatted like 'Thu, Feb 8'
      return new Date().toLocaleDateString('en-US', {
        weekday: 'short',
        month: 'short',
        day: 'numeric'
      })
    },
    tapTimer(category) {
      this.currentTime = 0
      this.toggleTimer(category)
    },
    updateActive() {
      if (!this.active.category) return
      this.currentTime = Date.now() - this.active.start
    }
  },
  mounted() {
    this.updateActive()
    this.updateInterval = setInterval(this.updateActive, 100)
  },
  destroyed() {
    clearInterval(this.updateInterval)
  }
}
</script>

<style lang="sass">
@keyframes pulse
  to
    transform: scale(1.1)

#home
  #date
    font-size: 2.5rem
    line-height: 1em

  #tips
    color: #888

  #tracking
    margin: 1em auto
    max-width: 400px
    display: flex
    flex-flow: row wrap
    justify-content: space-around

    > div
      margin-top: 1em
      flex: 33%
      text-align: center
      transition: opacity 300ms ease

      &.active
        opacity: 1
        animation: pulse 1.618s alternate ease-in-out infinite

        .circle
          // background-color: #999
          color: #fff

      &.inactive
        opacity: 0.5

      .circle
        display: inline-flex
        justify-content: center
        align-items: center
        color: #888
        width: 5em
        height: 5em
        border: 2px solid #999
        border-radius: 50%
        transition: all 300ms ease

  #cancel
    display: inline-block
    padding: 0.5em 1em
    margin: 0 auto
    border: 2px solid hsl(0, 80%, 60%)
    background: #fff
    color: hsl(0, 80%, 60%)
    border-radius: 1em
    transition: all 300ms ease
    opacity: 0.7

    &.confirm
      background: hsl(0, 80%, 60%)
      color: #fff
      opacity: 1
</style>
