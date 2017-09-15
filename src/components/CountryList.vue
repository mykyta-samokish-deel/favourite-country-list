<template lang="pug">
  transition-group.country-list(v-if='items.length'
    :class='isFavourite && "fav"'
    tag='ul'
    name='list'
  ): country-item(v-for='item in definedList' :key='item.name' :item='item' :isFavourite='isFavourite')
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
    isFavourite: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    definedList () {
      return this.isFavourite ? this.items.filter(i => i.favourite) : this.items
    }
  },
  beforeCreate () {
    console.log('CountryList.vue has started loading!')
  },
  mounted () {
    const randomItem = Math.random().toString().substr(2, 3)
    console.time(`CountryList.vue ${randomItem} finished loading`)
    this.$nextTick(() => {
      console.timeEnd(`CountryList.vue ${randomItem} finished loading`)
    })
  }
}
</script>

<style lang="styl" scoped>
.country-list
  flex 1 0 100%
  background-color inherit
  width 100%

.fav
  margin-top -8px

.list-enter-active, .list-leave-active
  transition all .5s

.list-enter, .list-leave-to
  opacity 0
  transform translateX(-2000px)
</style>
