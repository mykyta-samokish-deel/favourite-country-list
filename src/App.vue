<template lang="pug">
  #app
    .container
      header
        h1 My countries
      search-input
      main
        country-list(:items='paginate(list)')
      .pagination
        a.paginator(@click='prevPage()' :style="toggleButtonColorState(hasPrev)") Previous Page
        a.paginator(@click='nextPage()' :style="toggleButtonColorState(hasNext)") Next Page

    .container
      header
        h1 My favorites
        img.header--star(:src='require("@/assets/star.svg")')
      main
        country-list(:items='list' :isFavourite='true')
</template>

<script>
import SearchInput from '@/components/SearchInput'
import CountryList from '@/components/CountryList'
import bus from '@/bus'

const itemInArray = (arr, item) => arr.some(i => i.name === item.name)

export default {
  name: 'app',
  components: {
    SearchInput,
    CountryList
  },
  data () {
    return {
      list: [],
      currentPage: 0,
      hasPrev: false,
      hasNext: false
    }
  },
  computed: {
    favouritedList () {
      return this.list.filter(i => i.favourite)
    },
    pagesAmount () {
      if (!this.list.length) return 1
      return Math.ceil(this.list.length / 10)
    }
  },
  methods: {
    toggleButtonColorState (state) {
      return { backgroundColor: state ? 'rgba(73, 144, 226, 0.8)' : '#ededed' }
    },
    prevPage () {
      if (this.currentPage === 0) return
      this.currentPage -= 1
    },
    nextPage () {
      if (this.currentPage === this.pagesAmount - 1) return
      this.currentPage += 1
    },
    paginate (list) {
      return list.slice(this.currentPage * 10, (this.currentPage + 1) * 10)
    }
  },
  watch: {
    list (newVal, oldVal) {
      if (!newVal.length) return

      if (newVal.length > 10 && this.currentPage < (this.pagesAmount - 1)) this.hasNext = true
      if (newVal.length < 11) {
        this.currentPage = 0
        this.hasNext = false
      }

      const newElem = newVal[0]
      if (!itemInArray(oldVal, newElem)) {
        console.log(`A new element added: ${newElem.name}.`)
      }
    },
    currentPage (n) {
      this.hasPrev = n !== 0
      this.hasNext = n !== (this.pagesAmount - 1)
    }
  },
  beforeCreate () {
    console.log('App.vue has started loading!')
    console.time('App.vue loaded!')
  },
  mounted () {
    this.hasNext = this.currentPage !== this.pagesAmount - 1

    this.$nextTick(() => {
      console.timeEnd('App.vue loaded!')
    })

    bus.$on('itemToRemove', i => {
      this.$set(i, 'favourite', false)
      const itemIndex = this.list.findIndex(item => i.name === item.name)
      this.list.splice(itemIndex, 1)
    })

    bus.$on('items', item => {
      if (itemInArray(this.list, item)) {
        console.error(`${item.name} is already in a list 😕`)
        return
      }
      this.list = [item, ...this.list]
    })
  }
}
</script>

<style lang="styl">
*
  margin 0
  padding 0

#app
  display flex
  align-items center
  justify-content center
  font-family 'Helvetica Neue', Arial, sans-serif
  background-color #fafafa
  height 100vh
  width 100vw

.container
  display flex
  flex-direction column
  background-color #ffffff
  box-shadow 0 1px 8px 0 rgba(0, 0, 0, 0.1)
  border-radius 2px
  width 435px
  height 588px
  margin 20px

main
  flex 1
  display flex

header
  display flex
  align-items center
  background-color inherit
  margin 24px
  margin-bottom 16px

  h1
    font-size 32px
    font-weight 500
    color black

  .header--star
    margin-left 10px
    width 29.4px
    height 28px

.pagination
  display flex
  height 70px
  justify-content space-between
  align-items flex-start
  padding 0 24px

  .paginator
    text-decoration none
    cursor pointer
    font-size 16px
    line-height 1.12
    padding 6px 10px
    color white
    background-color #4990e2
    border-radius 4px
    border-width 0
    transition background-color 250ms

    &:hover
      background-color darken(#4990e2, 15%)
</style>
