<template>
  <div>
    <!-- TODO: Used in router only, but perhaps we should re-think the loader strategy in general -->
    <vue-progress-bar />
    <AppLoading v-if="isLoading || !featureFlagsReady" />
    <template v-else>
      <Navbar v-if="showNavbar" />
      <div class="body bg-white d-flex">
        <router-view class="flex-1" />
      </div>
    </template>
    <portal-target name="modal-alt" />
    <portal-target name="modal" />
    <ApiError
      v-if="apiError"
      v-on-clickaway="unsetError"
    />
    <AppSnackbar
      :opened="!!Object.keys(snackbar).length"
      :variant="snackbar.variant"
    >
      <!-- `height` is because <p> collapses when `snackbar.text` disappears, causing animation flaw. -->
      <p
        class="text-white text-center mb-0"
        :style="{ height: '24px' }"
      >{{ snackbar.text }}</p>
    </AppSnackbar>
  </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'
import Navbar from '@/components/TheNavbar'
import { mixin as clickaway } from 'vue-clickaway'
import useFeatureFlags from './components/composables/useFeatureFlags'
const ApiError = () => import('@/components/ApiError')

export default {
  setup () {
    const { ready: featureFlagsReady } = useFeatureFlags()
    return { featureFlagsReady }
  },
  mixins: [ clickaway ],
  components: {
    Navbar,
    ApiError,
  },
  computed: {
    ...mapState([ 'loggedIn', 'isLoading' ]),
    ...mapState('ui', [ 'snackbar' ]),
    ...mapGetters('ui', [ 'modalOnTop' ]),
    ...mapGetters([
      'school',
      'formSchool',
      'products',
      'apiError',
    ]),
    showNavbar () {
      return (this.$route.name &&
        ![
          'PreferenceFormLoader',
          'Login',
          'ResetPassword',
          'SchoolLogin',
          'BrowserSupport',
          'cmPublicProposal',
        ].includes(this.$route.name))
    },
  },
  watch: {
    modalOnTop (modal) {
      document.body.classList.remove('modal-open')
      document.body.classList.remove('modal-inline')
      if (modal) {
        if (!modal.stacked) document.body.classList.add('modal-open')
        if (modal.inline) document.body.classList.add('modal-inline')
      }
    },
  },
  methods: {
    unsetError () {
      this.$store.commit('setApiError', null)
    },
  },
}
</script>

<style src="@/css/app.scss" lang="scss"></style>
<style src="@/css/animations.css"></style>
<style src="@shared/css/colors.scss" lang="scss"></style>
<style src="@shared/css/nav.css"></style>
<style src="@/css/v-tooltip.css"></style>
<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
<style src="@/css/multiselect.css"></style>
<style src="@/css/multiselect_required.css"></style>

<style scoped>
.body {
  width: 100%;
  height: 100%;
  min-height: calc(100vh - 72px);
}
</style>
