<template>
  <div class="b-slot b-slot b-on-boarding-tips-step-4"
    @mouseleave.self="mouseleave"
    @mouseover="mouseover"
    :class="[
      { '_hover' : hoverBy === 'block' }
    ]"
    :style="{
      '--mobile-slot-flex-direction': mediaStyles['is-mobile']['flex-direction'],
      '--mobile-slot-align-items': mediaStyles['is-mobile']['align-items'],
      '--mobile-slot-justify-content': mediaStyles['is-mobile']['justify-content'],
      '--mobile-slot-background-color': mediaStyles['is-mobile']['background-color'],
      '--mobile-slot-background-image': mediaStyles['is-mobile']['background-image'],
      '--mobile-slot-background-position': mediaStyles['is-mobile']['background-position'],
      '--mobile-slot-background-repeat': mediaStyles['is-mobile']['background-repeat'],
      '--mobile-slot-background-size': mediaStyles['is-mobile']['background-size'],
      '--mobile-slot-background-attachment': mediaStyles['is-mobile']['background-attachment'],
      '--mobile-slot-margin-top': mediaStyles['is-mobile']['margin-top'],
      '--mobile-slot-margin-right': mediaStyles['is-mobile']['margin-right'],
      '--mobile-slot-margin-bottom': mediaStyles['is-mobile']['margin-bottom'],
      '--mobile-slot-margin-left': mediaStyles['is-mobile']['margin-left'],
      '--mobile-slot-padding-top': mediaStyles['is-mobile']['padding-top'],
      '--mobile-slot-padding-right': mediaStyles['is-mobile']['padding-right'],
      '--mobile-slot-padding-bottom': mediaStyles['is-mobile']['padding-bottom'],
      '--mobile-slot-padding-left': mediaStyles['is-mobile']['padding-left']
    }
  ">
    <div
      @mouseover.self="mouseover"
      @mouseleave.self="mouseleave"
      class="b-slot__settings"
    >
      <span
        @click.stop="showSandboxSidebar($event, 'SlotSettings')"
        class="b-slot__settings-item b-slot__settings-item-settings"
        >
          <icon-base name="cog" fill="white" />
      </span>
      <span
        @click.stop="showSandboxSidebar($event, 'SlotBackground')"
        class="b-slot__settings-item b-slot__settings-item-slot-bg"
        >
          <icon-base name="background" fill="white" />
      </span>
      <span
        @click.stop="showSandboxSidebar($event, 'Slot')"
        class="b-slot__settings-item b-slot__settings-item-add-el"
        >
          <icon-base name="plus" fill="white" />
      </span>
    </div>
    <slot/>
  </div>
</template>

<script>
import { mapState, mapMutations, mapActions } from 'vuex'
import { merge } from 'lodash-es'

export default {
  name: 'Sandbox',

  inject: ['$section'],

  props: {
    containerPath: String,
    componentsPath: String
  },

  computed: {
    ...mapState('Sidebar', [
      'sandbox',
      'settingObjectOptions',
      'settingObjectSection',
      'device',
      'hoverBy'
    ]),

    ...mapState('OnBoardingTips', [
      'isShowTips',
      'stepTips'
    ]),

    slot () {
      return this.settingObjectSection.get(this.sandbox.container) || {}
    },

    isMobile () {
      return this.device === 'is-mobile'
    },

    styles () {
      return this.$section.get(this.containerPath).styles
    },

    mediaStyles: {
      get () {
        let device = 'is-mobile'
        let media = { 'is-mobile': {} }
        let stylesMedia = this.$section.get(this.containerPath).media

        if (stylesMedia === undefined) {
          stylesMedia = media
        }

        if (stylesMedia[device]) {
          for (let key in this.styles) {
            media[device][key] = stylesMedia[device][key] !== undefined ? stylesMedia[device][key] : this.styles[key]
          }
        } else {
          media[device] = this.styles
        }

        return media
      },
      set (value) {
        this.settingObjectSection.set(this.sandbox.container, merge({}, this.slot, {
          media: value
        }))
      }
    },

    direction () {
      return this.getPropValue('flex-direction')
    },

    align () {
      return this.getPropValue('align-items')
    }
  },

  created () {
    this.styles['flex-direction'] = this.styles['flex-direction'] || this.direction
    this.styles['align-items'] = this.styles['align-items'] || this.align
    this.styles['align-items'] = this.styles['align-items'] || this.align
  },

  methods: {
    ...mapMutations('Sidebar', [
      'setSandboxPaths',
      'setSection',
      'isAddSectionExpanded'
    ]),
    ...mapActions('Sidebar', [
      'toggleSidebar',
      'setControlPanel',
      'setElement',
      'setSettingObject',
      'setHoverBy'
    ]),

    showSandboxSidebar (e, openElBar) {
      this.isAddSectionExpanded(false)

      this.setElement(this.$slots.default[0].elm)

      // --- clear active classes
      document.querySelectorAll('.b-draggable-slot.active')
        .forEach(el => el.classList.remove('active'))

      let options = this.$section.get(this.containerPath)
      options.name = this.containerPath

      this.setSettingObject({
        options,
        type: 'slot',
        label: 'slot'
      })

      this.setSection(this.$section)
      this.setSandboxPaths({
        components: this.componentsPath,
        container: this.containerPath
      })

      setImmediate(() => {
        let target = e.target.closest('.b-slot__settings-item').nextElementSibling
        if (target) {
          target.classList.add('active')
        }
      })

      this.toggleSidebar(true)

      this.setControlPanel(openElBar)
    },

    getPropValue (prop) {
      let s = ''

      if (this.isMobile && this.mediaStyles[`${this.device}`] && this.mediaStyles[`${this.device}`][prop]) {
        s = this.mediaStyles[`${this.device}`][prop]
      } else {
        s = this.styles[prop]
      }

      return s
    },

    mouseover () {
      this.setHoverBy('block')
    },

    mouseleave () {
      this.setHoverBy(null)
    }
  }
}
</script>

<style lang="sass" scoped>
</style>
