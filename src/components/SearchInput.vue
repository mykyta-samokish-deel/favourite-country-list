<template lang="pug">
  transition(name='slidey')
    .input-form
      .input-field(style='position: relative')
        input#textInput(
          type='text',
          name='textInput',
          v-model='countryField',
          placeholder='Search for country',
          autocomplete='off',
          @keyup='keyupEvent',
          @focus='focus',
          autofocus
        )
        ul#results(v-show='results.length')
          li.search__result(
            v-for='result in filteredResults'
            :key='result'
            @click='selectItem'
          ) {{ result }}
      button.addCountry(@click.prevent='addToList') Add +
</template>

<script>
import bus from '@/bus'

export default {
  name: 'search-input',
  data () {
    return {
      countryField: '',
      results: []
    }
  },
  computed: {
    filteredResults () {
      return this.results.filter((v, i, s) => s.indexOf(v) === i)
    }
  },
  methods: {
    focus (e) {
      this.countryField = e.target.value = ''
    },
    selectItem (e) {
      this.countryField = e.target.innerText
    },
    keyupEvent (e) {
      try {
        if (![38, 40, 13].includes(e.keyCode)) return

        const activeClass = 'search__result--active'
        const current = document.querySelector(`.${activeClass}`)
        const items = document.querySelectorAll('.search__result')
        let next

        if (e.keyCode === 40 && current) {
          next = current.nextElementSibling || items[0]
          e.target.value = next.innerText
        } else if (e.keyCode === 40) {
          next = items[0]
          e.target.value = next.innerText
        } else if (e.keyCode === 38 && current) {
          next = current.previousElementSibling || items[items.length - 1]
          e.target.value = next.innerText
        } else if (e.keyCode === 38) {
          next = items[items.length - 1]
          e.target.value = next.innerText
        } else if (e.keyCode === 13) {
          if (current && current.innerText) {
            this.countryField = e.target.value = current.innerText
            bus.$emit('items', {
              name: this.countryField,
              favourite: false
            })
          }
          return
        }

        if (current) {
          current.classList.remove(activeClass)
        }
        next.classList.add(activeClass)
      } catch (e) { /* Error handling goes here */ }
    },
    addToList () {
      if (this.countryField) {
        bus.$emit('items', {
          name: this.countryField,
          favourite: false
        })
      }
    }
  },
  watch: {
    countryField (newVal) {
      if (newVal.length === 0) {
        this.results = []
        return
      }

      const countryField = document.getElementById('textInput')
      const service = new google.maps.places.AutocompleteService() // eslint-disable-line
      service.getQueryPredictions({ input: countryField.value }, (predictions, status) => {
        if (status !== 'OK') {
          this.countryField = ''
          console.warn('Oops, the request failed because of zero results')
          return
        }

        predictions.map(prediction => {
          if (prediction.hasOwnProperty('types') && prediction.types.includes('country')) {
            this.results.unshift(prediction.description)
          }
          return null
        })
      })
    },
    results (val) {
      if (this.results.includes(this.countryField)) this.results = []
    }
  },
  beforeCreate () {
    console.log('SearchInput.vue has started loading!')
    console.time('SearchInput.vue loaded!')
  },
  mounted () {
    this.$nextTick(() => {
      console.timeEnd('SearchInput.vue loaded!')
    })
  }
}
</script>

<style lang="styl" scoped>
.input-form
  display flex
  justify-content space-between
  margin 0 24px
  margin-bottom 8px

  .input-field
    input[type=text]
      width 100%
      font-size 18px
      margin-right 1em
      height 2em
      background-color #ededed
      border 0
      padding 0 .66em

.addCountry
  cursor pointer
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

.slidey-enter-active, .slidey-leave-active
  transition transform .5s

.slidey-enter, .slidey-leave-to
  transform translateX(-100px)

#results
  position absolute
  z-index 2
  display flex
  flex-direction column
  align-items stretch
  box-shadow rgba(0, 0, 0, 0.3) 0px 2px 6px 0px
  top 36px
  background-color white
  width 100%

.search__result
  list-style none
  background-color inherit
  border-bottom 1px solid #ededed
  box-shadow rgba(0, 0, 0, 0.3) 0px 2px 6px 0px
  padding 8px 10px
  width 100%
  transition background-color 150ms

  &:hover
    background-color lighten(#ededed, 40%)

.search__result--active
  background-color lighten(#ededed, 40%)

</style>
