<template>
  <div class="b-panel">
    <h6 class="b-panel__title">
      Columns
    </h6>

    <IndicatorPlatform />

    <div class="b-panel__layout _top-9">
      <div class="layout _top-2">
        <base-scroll-container>
          <div class="layout-padding _pr-0">
            <!-- Columns settings -->
            <div class="b-panel__control" v-if="!isMobile">
              <base-caption>
                Columns settings
              </base-caption>
              <div class="b-panel__control">
                <div class="b-panel__col">
                  <control-section-columns/>
                </div>
              </div>
              <div class="b-panel__control">
                <div class="b-panel__col">
                  <control-vertical-align/>
                </div>
              </div>
            </div>

            <disabled-mobile-mode v-else />
          </div>
        </base-scroll-container>
      </div>
    </div>
  </div>
</template>

<script>
import ControlSectionColumns from './../controls/TheControlSectionColumns.vue'
import ControlVerticalAlign from './../controls/TheControlVerticalAlign.vue'
import IndicatorPlatform from '../IndicatorPlatform'
import DisabledMobileMode from '../DisabledMobileMode'
import { mapState } from 'vuex'

export default {
  name: 'ThePanelSectionColumnsSettings',

  components: {
    IndicatorPlatform,
    ControlSectionColumns,
    ControlVerticalAlign,
    DisabledMobileMode
  },

  computed: {
    ...mapState('Sidebar', [
      'isMobile'
    ])
  }
}
</script>
