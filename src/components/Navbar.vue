<template>
  <nav>
    <ul>
      <router-link v-if="!alt"
        tag="li"
        to="/">Timers</router-link>
      <router-link v-if="alt"
        tag="li"
        to="/alt">Timers</router-link>
      <router-link tag="li"
        to="/insights">Insights</router-link>
      <router-link tag="li"
        to="/timeline">Timeline</router-link>
    </ul>
  </nav>
</template>

<script>
export default {
  data() {
    return {
      alt: !!this.$route.meta.alt
    }
  }
}
</script>

<style lang="sass">
nav
  position: fixed
  bottom: 0
  width: 100%
  background-color: #fff

  ul
    list-style: none
    margin: 0
    padding: 0
    display: flex

  li
    flex: 1
    padding: 1em
    border-top: 1px solid #999

    &:not(:last-child)
      border-right: 1px solid #999

    &.router-link-exact-active
      background-color: #ff9901
      color: #fff
</style>
