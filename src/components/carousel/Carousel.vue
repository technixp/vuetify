<template lang="pug">
  div(class="slider")
    div(class="slider__left")
      v-btn(icon v-on:click.native.stop="prev")
        v-icon chevron_left

    div(class="slider__right")
      v-btn(icon v-on:click.native.stop="next")
        v-icon chevron_right

    div(class="slider__controls")
      v-btn(
        class="slider__controls__item"
        icon
        v-bind:class="{ 'slider__controls__item--active': index === current }"
        v-for="(item, index) in items"
        v-on:click.native.stop="select(index)"
      )
        v-icon {{ icon }}
    slot
</template>

<script>
  export default {
    name: 'carousel',

    data () {
      return {
        current: null,
        items: [],
        slideInterval: {},
        reverse: false
      }
    },

    props: {
      cycle: {
        type: Boolean,
        default: true
      },

      icon: {
        type: String,
        default: 'fiber_manual_record'
      },

      interval: {
        type: Number,
        default: 6000
      }
    },

    computed: {
      defaultState () {
        return {
          current: null,
          reverse: false
        }
      }
    },

    watch: {
      current () {
        // Evaluate items when current changes to account for
        // dynamic changing of children
        this.items = this.$children.filter(i => {
          return i.$el.classList && i.$el.classList.contains('slider__item')
        })

        this.items.forEach(i => i.open(this.items[this.current]._uid, this.reverse))

        if (this.cycle) {
          clearInterval(this.slideInterval)
          this.startInterval()
        }
      }
    },

    mounted () {
      this.init()
    },

    methods: {
      init () {
        this.current = 0
      },

      next () {
        this.reverse = false

        if (this.current + 1 === this.items.length) {
          return (this.current = 0)
        }

        this.current++
      },

      prev () {
        this.reverse = true

        if (this.current - 1 < 0) {
          return (this.current = this.items.length - 1)
        }

        this.current--
      },

      select (index) {
        this.reverse = index < this.current
        this.current = index
      },

      startInterval () {
        this.slideInterval = setInterval(this.next, this.interval)
      }
    }
  }
</script>