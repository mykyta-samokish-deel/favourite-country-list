<template lang="pug">
  transition-group(name='list' tag='ul' v-if='items.length')
    country-item(v-for='item in definedList' :key='item.name' :item='item' :isFavourite='isFavourite')
</template>

<script>
export default {
  name: 'country-list',
  components: {
    CountryItem: () => import('./CountryItem')
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
