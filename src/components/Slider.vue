<template>
  <div>
    <!-- Nav Links -->
    <div ref="slider" class="navMenu" :style="[menuDirection, menuWidth, menuLeft, menuRight]">
      <a href="javascript:void(0)" class="closebtn" @click="closeMenu()">
        <slot name="closeIcon">
          &times;
        </slot>
      </a>
      <slot name="options">
          <div v-for="link in links" :key="link.id" :class="{'parent': 'link.links'}">
            <a :href="link.url" v-if="!link.links || link.links.length === 0">{{ link.text }}</a>
            <div v-for="link in link.links" :key="link.id" class="sublink">
                <a :href="link.url">{{ link.text }}</a>
            </div>
          </div>
      </slot>
    </div>
    <!-- Hamburger Menu -->
    <nav ref="menuIcon" class="navIcon" :style="iconDirection" v-if="burgerButtonVisible">
      <slot name="menuIcon">
        <a href="javascript:void(0)" @click="toggleMenu()" data-test-ref="navMenuLink">
          <svg width="30" height="30">
            <path d="M0,5 30,5" :stroke="styles['menu-icon-color']" stroke-width="5"/>
            <path d="M0,14 30,14" :stroke="styles['menu-icon-color']" stroke-width="5"/>
            <path d="M0,23 30,23" :stroke="styles['menu-icon-color']" stroke-width="5"/>
          </svg>
        </a>
      </slot>
    </nav>
  </div>
</template>

<script>
import styles from '@/assets/sass/variables.scss'
import utilities from '@/js/utilities'
export default {
  name: 'slider',
  props: {
    width: {
      type: Number,
      required: false,
      default: 250
    },
    format: {
      type: String,
      required: false,
      default: 'overlay'
    },
    direction: {
      type: String,
      required: false,
      default: 'left'
    },
    opacity: {
      type: Number,
      required: false,
      default: 0
    },
    links: {
      type: Array,
      required: true
    },
    burgerButtonVisible: {
      type: Boolean,
      required: false,
      default: true
    },
    open: {
      type: Boolean,
      required: false,
      default: false
    }
  },
  data () {
    return {
      styles: styles,
      menuWidth: {
        'width': 0
      },
      menuLeft: {
        'left': 'auto'
      },
      menuRight: {
        'right': 'auto'
      }
    }
  },
  watch: {
    open: function () {
      this.toggleMenu()
    }
  },
  computed: {
    menuWidthSize () {
      return this.format === 'full' ? window.innerWidth : this.width
    },
    menuDirection () {
      return this.direction === 'right' ? { 'right': 0 } : { 'left': 0 }
    },
    iconDirection () {
      return this.direction === 'right' ? { 'float': 'right' } : { 'float': 'left' }
    },
    app () {
      return document.getElementById('app')
    }
  },

  mounted () {
    this.menuWidth.width = this.menuWidthSize + 'px'
    if (this.direction === 'left') {
      this.menuLeft.left = this.menuWidthSize * -1 + 'px'
    } else {
      this.menuRight.right = this.menuWidthSize * -1 + 'px'
    }
  },

  methods: {
    openMenu () {
      if (this.opacity) {
        document.body.style.backgroundColor = utilities.hexToRGB(styles['background-color'], this.opacity)
      }
      this.setFormat()
    },
    setFormat () {
      const width = this.width.toString() + 'px'
      if (this.format === 'overlay') {
        if (this.direction === 'left') {
          this.menuLeft.left = 0
        } else if (this.direction === 'right') {
          this.menuRight.right = 0
        }
      } else if (this.format === 'full') {
        if (this.direction === 'left') {
          this.menuLeft.left = 0
        } else if (this.direction === 'right') {
          this.menuRight.right = 0
        }
      } else {
        this.menuLeft.left = 0
        if (this.app) {
          if (this.direction === 'right') {
            this.app.style.marginRight = width
            this.app.style.transition = 'margin-right .5s'
          } else {
            this.app.style.marginLeft = width
            this.app.style.transition = 'margin-left .5s'
          }
        } else {
          console.warn(`[Slider] The format was set to '${this.format}', but there is no element with an id 'app'. Add id='app' to the element to move.`)
        }
      }
    },
    closeMenu () {
      if (this.direction === 'left') {
        this.menuLeft.left = (this.menuWidthSize * -1) + 'px'
      } else if (this.direction === 'right') {
        this.menuRight.right = (this.menuWidthSize * -1) + 'px'
      }
      if (this.app) {
        this.app.style.marginLeft = '0'
        this.app.style.marginRight = '0'
      }
      if (this.opacity) {
        document.body.style.backgroundColor = styles['background-color']
      }
    },
    toggleMenu () {
      if (this.menuLeft.left === 0) {
        this.closeMenu()
      } else {
        this.openMenu()
      }
    }
  }
}
</script>

<style lang="scss" scoped>
  @import '@/assets/sass/variables.scss';

  body {
    background-color: $background-color;
    transition: background-color .5s;
  }
  #app {
    transition: margin-left .5s;
    transition: margin-right .5s;
    padding: 20px;
  }
  .navMenu {
    font-family: $font-family-sans-serif;
    height: 100%;
    width: 0;
    position: fixed;
    z-index: 1;
    top: 0;
    background-color: $menu-background;
    overflow-x: hidden;
    padding-top: 60px;
    transition: 0.5s;
    a {
      padding: 8px 8px 8px 32px;
      text-decoration: none;
      font-size: 25px;
      color: $menu-text;
      display: block;
      transition: 0.3s;
      &:hover {
        color: $menu-text-hover;
      }
    }
    .parent {

    }
    .sublink {
        margin-left: 20px;
        line-height: 0.8;
        a {
            font-size: 20px;
        }
  }
    .closebtn {
      position: absolute;
      top: 0;
      right: 25px;
      font-size: 36px;
      margin-left: 50px;
    }
  }
  @media screen and (max-height: 450px) {
    .navMenu {
      padding-top: 15px;
      a {
        font-size: 18px;
      }
    }
  }
</style>
