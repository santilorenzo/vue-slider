<template>
  <div id="app">
    <slider
      :width="300"
      format="overlay"
      direction="left"
      :opacity="0.15"
      :links="links"
      :burger-button-visible="false"
      :open="isOpen"
    >
    </slider>

    <button @click.prevent="toggleMenu">Toggle menu</button>

  </div>
</template>

<script>
import Slider from './components/Slider'
export default {
  name: 'app',
  components: {
    'slider': Slider
  },
  data: function () {
    return {
      isOpen: false,
      links: [
        {
          'id': 1,
          'text': 'Link 1',
          'url': 'https://github.com'
        },
        {
          'id': 4,
          'text': 'Link 3',
          'url': 'https://github.com',
          links: [
            { 'id': 1, 'text': 'Link 1', 'url': 'https://github.com' },
            { 'id': 2, 'text': 'Link 2', 'url': 'https://github.com' },
            { 'id': 3, 'text': 'Link 3', 'url': 'https://github.com' }
          ]
        }
      ]
    }
  },
  methods: {
    toggleMenu: function () {
      this.isOpen = !this.isOpen
    }
  }
}
</script>

<style lang="scss">
  #app {
    padding: 2em;
  }
</style>
