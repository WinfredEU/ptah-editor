<template>
  <div class="b-panel">
    <h6 class="b-panel__title">
      Products
    </h6>

    <IndicatorPlatform />

    <div class="b-panel__layout _top-9">
      <div class="layout _top-2">
        <base-scroll-container v-if="!isMobile">
          <div class="layout-padding _pr-0">
            <!-- Columns settings -->
            <div class="b-panel__control">
              <base-caption>
                Products settings
              </base-caption>
              <div class="b-panel__control">
                <div class="b-panel__col">
                  <control-section-products/>
                </div>
              </div>
              <div class="b-panel__control">
                <div class="b-panel__col">
                  <control-vertical-align
                    :propAlign="`align-items`"
                  />
                </div>
              </div>
            </div>
          </div>
        </base-scroll-container>

        <disabled-mobile-mode v-else />
      </div>
    </div>
  </div>
</template>

<script>
import ControlSectionProducts from './../controls/TheControlSectionProducts.vue'
import ControlVerticalAlign from './../controls/TheControlVerticalAlign.vue'
import IndicatorPlatform from '../IndicatorPlatform'
import DisabledMobileMode from '../DisabledMobileMode'
import { mapState } from 'vuex'

export default {
  name: 'ThePanelSectionProductsColumnsSettings',

  components: {
    IndicatorPlatform,
    ControlSectionProducts,
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
