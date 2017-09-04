<template lang="pug">
  #app
    .container
      header
        span {{ $route.name }}: {{ filteredList.length }}
        router-link.button(to='/favourite' v-if='isRoot') Favs
        router-link.button(to='/' v-else) Home
      search-input(v-if='isRoot')
      country-list(:items='paginate(filteredList)')
      .pagination
        a.button(@click='prevPage()' :style="toggleButtonColorState(hasPrev)") Previous Page
        span page {{ currentPage + 1 }}/{{ pagesAmount }}
        a.button(@click='nextPage()' :style="toggleButtonColorState(hasNext)") Next Page
</template>

<script>
import SearchInput from '@/components/SearchInput'
import CountryList from '@/components/CountryList'
import bus from '@/bus'

const itemInArray = (arr, item) => arr.some(i => i.name === item.name)

export default {
  name: 'app',
  components: { SearchInput, CountryList },
  data () {
    return {
      list: [],
      currentPage: 0,
      hasPrev: false,
      hasNext: false
    }
  },
  computed: {
    isRoot () {
      return this.$route.path === '/'
    },
    favouritedList () {
      return this.list.filter(i => i.favourite)
    },
    filteredList () {
      return this.isRoot ? this.list : this.favouritedList
    },
    pagesAmount () {
      if (!this.filteredList.length) return 1
      return Math.ceil(this.filteredList.length / 10)
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
    '$route' (to, from) {
      if (to !== from) this.currentPage = 0
      this.hasNext = this.currentPage !== this.pagesAmount - 1
    },
    currentPage (n, o) {
      this.hasPrev = n !== 0
      this.hasNext = n !== this.pagesAmount - 1
    },
    list (newVal, oldVal) {
      if (!newVal.length) return

      if (newVal.length > 10) this.hasNext = true
      if (newVal.length < 11) this.hasNext = false

      const newElem = newVal[newVal.length - 1]
      if (!itemInArray(oldVal, newElem)) {
        console.log(`A new element added: ${newElem.name}.`)
      }
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
  font-family 'Neue', Helvetica, Arial, sans-serif
  -webkit-font-smoothing antialiased
  -moz-osx-font-smoothing grayscale
  color #2c3e50
  display flex
  flex-direction column
  background-color white
  margin 0 auto
  width 100%

.container
  display flex
  flex-direction column
  background-color inherit

  @media (min-width:801px)
    width 60vw
    margin 50px auto

header
  display flex
  align-items center
  justify-content space-between
  padding 24px
  padding-bottom 16px
  background-color inherit
  color black
  font-weight 500

  span
    display block
    position relative
    font-size 32px
    font-size 5.9vw
    line-height 1
    letter-spacing .02em
    font-weight 500
    box-sizing border-box

.pagination
  display flex
  justify-content space-between
  align-items center
  padding 24px
  padding-top 0

  span
    text-align center

.button
  cursor pointer
  text-decoration none
  border-radius 4px
  padding 8px 12px
  font-size 18px
  line-height 20px
  color white
  background-color rgba(73, 144, 226, 0.8)
  border-width 0
  transition background-color 250ms

  &:hover
    background-color rgba(73, 144, 226, 1)

</style>
