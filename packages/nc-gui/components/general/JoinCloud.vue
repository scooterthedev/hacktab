<script lang="ts" setup>
import type { CloudFeaturesType } from '~/lib/types'

const { $api } = useNuxtApp()

const { cloudFeatures: _cloudFeatures } = useEeConfig()

const isLoading = ref(false)

const error = ref(false)

/**
 * This hardcoded list will be used as fallback in case of api error
 * Todo: @ramesh udpate coming soon once that feature is available
 */
const descriptions: CloudFeaturesType[] = [
  {
    Title: 'SAML based Single Sign-On',
    Highlight: true,
  },
  {
    Title: 'Form view branding',
  },
  {
    Title: 'Personal views',
  },
  {
    Title: 'Extensions',
  },
  {
    Title: 'Scripts',
  },
  {
    Title: 'AI Integrations',
    Highlight: true,
  },
]

const cloudFeatures = computed(() => {
  if (error.value && !_cloudFeatures.value) return descriptions

  return _cloudFeatures.value
})

const onMouseover = async () => {
  if (isLoading.value) return

  if (cloudFeatures.value.length && !error.value) {
    isLoading.value = false
    return
  }

  error.value = false
  isLoading.value = true

  try {
    const res = await $api.utils.cloudFeatures()

    _cloudFeatures.value = res
  } catch (e: any) {
    error.value = true
    console.error(await extractSdkResponseErrorMsg(e))
  } finally {
    isLoading.value = false
  }
}
</script>

<style lang="scss" scoped>
.nc-plan-description-gradient {
  @apply absolute rounded-[30px] inset-0 z-0 pointer-events-none;

  background: linear-gradient(90deg, rgba(255, 255, 255, 0.2) 0%, rgba(252, 58, 198, 0.2) 47.08%, rgba(255, 255, 255, 0.2) 100%);
  filter: blur(2px);
}
</style>

<style lang="scss">
.nc-join-cloud-tooltip {
  @apply max-w-none;

  .ant-tooltip-inner {
    @apply !bg-transparent !p-0 rounded-2xl;
  }
  .ant-tooltip-arrow-content {
    @apply !bg-white;
  }
}
</style>
