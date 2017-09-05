<template lang="pug">
  transition-group(name='list' tag='ul' v-if='items.length')
    country-item(v-for='item in items' :key='item.name' :item='item' :removable='removable')
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
    },
    removable: {
      type: Boolean,
      default: true
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
  flex 1 0 100%
  margin-top 20px
  background-color inherit
  width 100%

.list-enter-active, .list-leave-active
  transition all .5s

.list-enter, .list-leave-to
  opacity 0
  transform translateX(-500px)
</style>
