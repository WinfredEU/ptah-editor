<template>
  <div class="b-builder-layout"
    :class="[
      {'_is-show-tips': isShowTips}
    ]"
  >
    <BuilderTopBar
      class="b-builder-layout__top-bar"
      :class="{
        'b-builder-layout__top-bar_down' : isShowSettingsPage
      }"
      @backToLandings="backToLandings"
      @preview="$emit('preview', $event)"
      @export="$emit('export', $event)"
      @save="$emit('save', $event)"
      :landingName="$store.state.name"
      :builder="builder"
    />
    <div class="b-builder-layout-content">
      <div class="b-builder-layout-content__main-left-menu"
        :class="{
          '_expanded': isExpanded,
          '_show-modal': isShowModal || isShowModalButton
        }"
      >
        <BuilderMainLeftMenu
          :builder="builder"
        />
      </div>
      <aside
        id="sidebar"
        class="b-builder-layout-content__sidebar"
        :class="{
          'b-builder-layout-content__sidebar_expanded': isExpanded,
          'b-builder-layout-content__sidebar_expanded-content': isShowSettingsPage,
          '_show-modal': isShowModal || isShowModalButton,
          '_ultra-wide': isUltraWide
        }"
      >
        <BuilderSidebar
          :builder="builder"
          :isExpanded="isExpanded"
        />
      </aside>

      <main class="b-builder-layout-content__main"
        :class="[
          {'b-builder-layout-content__main_expanded': isExpanded && !isShowSettingsPage},
          {'b-builder-layout-content__main_expanded-setting': isExpanded && isShowSettingsPage},
          {'b-builder-layout-content__main_show-modal': isShowModal || isShowModalButton},
          {'b-builder-layout-content__main_ultra-wide': isUltraWide }
        ]"
      >
        <base-scroll-container
          backgroundBar="#fff">
          <div class="b-builder-layout-content__main-layout" :class="device">
            <slot></slot>
          </div>
        </base-scroll-container>
      </main>
    </div>
    <div class="tips-layout" @mousedown.stop @click.stop />
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'
import BuilderMainLeftMenu from './BuilderMainLeftMenu'
import BuilderSidebar from './BuilderSidebar.vue'
import BuilderTopBar from './BuilderTopBar.vue'
import { sectionsGroups } from '@cscripts/sectionsGroups'

export default {
  name: 'BuilderLayout',

  provide () {
    let device = {}
    let isExpanded = {}
    Object.defineProperty(device, 'type', { enumerable: true, get: () => this.device })
    Object.defineProperty(isExpanded, 'status', { enumerable: true, get: () => this.isExpanded })

    return { device, isExpanded }
  },

  props: {
    builder: {
      required: true
    }
  },

  components: {
    BuilderSidebar,
    BuilderTopBar,
    BuilderMainLeftMenu
  },

  computed: {
    ...mapState('Sidebar', [
      'isExpanded',
      'device',
      'isShowModal',
      'isShowModalButton'
    ]),

    ...mapState('OnBoardingTips', [
      'isShowTips'
    ]),

    isShowSettingsPage () {
      return this.$route.path.split('/').indexOf('settings') > 0
    },

    isUltraWide () {
      return this.$route.meta.ultraWide
    }
  },

  watch: {
    device: function () {
      sectionsGroups()
    }
  },

  methods: {
    ...mapActions('Landing', [
      'saveStateHandler'
    ]),

    backToLandings () {
      this.$Progress.start()
      this.saveStateHandler(this.builder.export('JSON')).finally(() => {
        this.$Progress.finish()
        this.$router.push({ path: `/dashboard` })
      })
    }
  }
}
</script>

<style lang="sass" scoped>
@import '../../assets/sass/_colors.sass'
@import '../../assets/sass/_variables.sass'

$topBarHeight: 6rem

.b-builder-layout
  position: relative
  min-width: 98rem
  height: 100vh
  min-height: 60rem

  &__top-bar
    height: $topBarHeight
    background-color: $white

    position: absolute
    top: 0
    right: 0
    left: 0
    z-index: 1000

    transition: all .2s ease-out
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15)
  &._is-show-tips
    .tips-layout
      content: ''
      position: absolute
      top: 0
      right: 0
      bottom: 0
      left: 0
      background: rgba(#000000, 0.4)
      z-index: 1000

.b-builder-layout-content
  position: absolute
  top: 0
  right: 0
  bottom: 0
  left: 0

  display: flex
  align-items: stretch
  justify-content: flex-start

  width: 100%
  z-index: 0

  transition: all .2s ease-out
  &__main-left-menu
    order: 1
    width: 5rem
    transition: width 0.3s ease-in-out

    position: relative
    z-index: 11
    &:hover
      z-index: 11
      width: 9rem
    &._show-modal
      z-index: 11
    &._expanded
      width: 9rem

  &__sidebar
    width: 0
    position: relative
    z-index: 10
    padding: 6rem 0 0
    opacity: 0

    order: 2
    flex-shrink: 0
    transition: width 0.3s ease-in-out, opacity 0.3s ease-in-out
    &._show-modal
      z-index: 11
    &_expanded
      opacity: 1
      width: 30.5rem
      display: flex
      z-index: 11
      &-content
        z-index: 11
        width: 30.5rem
    &._ultra-wide
      width: 48rem
  &__main
    position: absolute
    top: $topBarHeight
    right: 0
    bottom: 0
    left: 5rem
    z-index: 10

    display: flex
    justify-content: stretch

    min-height: 5rem
    transition: all .2s ease-out
    background-color: #fafafa
    &:after
      content: ""
      position: absolute
      top: 0
      left: 0
      bottom: 0
      width: 0.8rem
      box-shadow: inset 2px 0 5px 0  rgba(0, 0, 0, 0.05)
      z-index: 20
    &_expanded
      left: 39.5rem
    &_expanded-setting
      left: 39.5rem
    &_show-modal
      z-index: 12
      &:after
        z-index: 4
    &_ultra-wide
      left: 55.5rem

    &-layout
      transition: width 0.2s
      margin: 0 auto
      position: relative
      z-index: 5
      height: calc(100vh - 6rem)

      &.is-desktop
        width: 100%
      &.is-laptop
        width: 120rem
        max-width: 100%
      &.is-mobile
        width: 37rem
        max-width: 100%

.b-builder-layout-content__main-left-menu:not(._expanded):hover ~ .b-builder-layout-content__main
  left: 9rem
</style>
