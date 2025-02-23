<template>
  <Container :class="prefix">
    <template #aside>
      <Scroll use-y-bar height="100%" :delta-y="60">
        <Menu
          v-model:active="currentMenu"
          marker-type="left"
          :class="`${prefix}__menu`"
          @select="selectGuide"
        >
          <MenuItem v-for="menu in menus" :key="menu.label" :label="menu.label">
            {{ t(`guides.${menu.i18n}`) }}
          </MenuItem>
        </Menu>
      </Scroll>
    </template>
    <main :class="`${prefix}__main`">
      <router-view v-slot="{ Component }">
        <transition name="vxp-fade" mode="out-in">
          <component :is="Component"></component>
        </transition>
      </router-view>
    </main>
    <Footer></Footer>
  </Container>
</template>

<script setup lang="ts">
import { inject, ref, watch } from 'vue'
import { useI18n } from 'vue-i18n'
import { useRoute, useRouter } from 'vue-router'

import Container from '../common/container.vue'
import Footer from '../common/footer.vue'
import { getGuideConfig } from '../router/guides'

const globalState = inject('globalState', { language: __ROLLBACK_LANG__ })

const prefix = 'guides'

const currentMenu = ref('')
const menus = getGuideConfig()

const { t } = useI18n({ useScope: 'global' })
const router = useRouter()
const route = useRoute()

watch(
  () => route.path,
  value => {
    if (value.startsWith(`/${globalState.language}/guides`)) {
      currentMenu.value = value.split('/')[3]
    }

    if (!currentMenu.value) {
      currentMenu.value = menus[0].label
    }
  },
  { immediate: true }
)

function selectGuide(label: string) {
  router.push(`/${globalState.language}/guides/${label}`)
}
</script>

<style lang="scss">
.guides {
  &,
  &__sider,
  &__content {
    position: relative;
    height: 100%;
  }

  &__sider {
    border-right: var(--vxp-border-light-2);
    transition: var(--vxp-transition-border);
  }

  &__menu {
    padding: 40px 0 20px;
    border-right: 0;
  }

  &__main {
    height: 100%;
  }
}
</style>
