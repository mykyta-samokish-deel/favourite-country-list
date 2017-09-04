<template lang='pug'>
  li.item(@mouseover='focus = true' @mouseleave='focus = false')
    span.item--name {{ item.name }}
    .item--icons
      img.item--icon.trash(
        :src='require("../assets/trash.svg")'
        @click='removeItem(item)'
        v-show='focus || mobileKeepAlive'
        v-if='$route.path === "/"'
      )
      img.item--icon.star(
        :src='getStarLink(item)'
        @click='addToFavourites(item)'
        v-show='item.favourite || focus || mobileKeepAlive'
      )
</template>

<script>
import bus from '@/bus'

export default {
  name: 'country-item',
  data () {
    return {
      focus: false
    }
  },
  props: {
    item: {
      type: Object,
      required: true
    },
    index: Number
  },
  computed: {
    mobileKeepAlive () {
      return /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)
    }
  },
  methods: {
    getStarLink (item) {
      return item.favourite ? require('../assets/star.svg') : require('../assets/star-empty.svg')
    },
    removeItem (item) {
      bus.$emit('itemToRemove', this.item)
      console.log(`Item removed: ${item.name}`)
    },
    addToFavourites (item) {
      this.$set(item, 'favourite', !item.favourite)
      if (item.favourite) {
        console.log(`Item added to favourites: ${item.name}`)
        return
      }
      console.log(`Item excluded from favourites: ${item.name}`)
    }
  }
}
</script>

<style lang='styl'>
.item
  display flex
  align-items center
  justify-content space-between
  list-style none
  background-color inherit
  transition background-color 400ms
  padding 8px 24px
  min-height 36px

  &:hover
    background-color rgba(0, 0, 0, 0.05)

.item--name
  font-size 18px
  color #4a4a4a

.item--icon:nth-child(2)
  margin-left 16.6px

.trash
  width 13.4px
  height 18px

.star
  width 21px
  height 20px
</style>
