<template lang="pug">
  transition-group(name='list' tag='ul' v-if='items.length')
    country-item(v-for='item in items' :key='item.name' :item='item')
  transition(v-else name='fade' mode='in-out')
    span.no-items No countries in a list for a moment 😕
</template>

<script>
import CountryItem from './CountryItem'

export default {
  name: 'country-list',
  components: {
    CountryItem
  },
  props: {
    items: {
      type: Array,
      required: true
    }
  },
  beforeCreate () {
    console.log('CountryList.vue has started loading!')
    console.time('CountryList.vue loaded!')
  },
  mounted () {
    this.$nextTick(() => {
      console.timeEnd('CountryList.vue loaded!')
    })
  }
}
</script>

<style lang="styl" scoped>
ul
  position relative
  margin 20px auto
  background-color white
  width 100%
  text-align left

.list-enter-active, .list-leave-active
  transition all .5s

.list-enter, .list-leave-to
  opacity 0
  transform translateX(-100px)

.no-items
  padding 30px
  font-size 20px
  text-align center

.fade-enter-active, .fade-leave-active
  transition opacity .5s

.fade-enter, .fade-leave-to
  opacity 0
</style>
