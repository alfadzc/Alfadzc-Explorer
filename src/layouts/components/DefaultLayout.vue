<script lang="ts" setup>
import { Icon } from '@iconify/vue';
import { computed, ref, onMounted } from 'vue';

// Components
import newFooter from '@/layouts/components/NavFooter.vue';
import NavbarThemeSwitcher from '@/layouts/components/NavbarThemeSwitcher.vue';
import NavbarSearch from '@/layouts/components/NavbarSearch.vue';
import ChainProfile from '@/layouts/components/ChainProfile.vue';
import Sponsors from '@/layouts/components/Sponsors.vue';

import { useDashboard } from '@/stores/useDashboard';
import { NetworkType } from '@/types/chaindata';
import { useBaseStore, useBlockchain } from '@/stores';

import NavBarI18n from './NavBarI18n.vue';
import NavBarWallet from './NavBarWallet.vue';
import type {
  NavGroup,
  NavLink,
  NavSectionTitle,
  VerticalNavItems,
} from '../types';
import dayjs from 'dayjs';
import AdBanner from '@/components/ad/AdBanner.vue';

// Loading screen
const loading = ref(true);
const fadeOut = ref(false);

onMounted(() => {
  window.scrollTo(0, 0);
  document.documentElement.style.overflow = 'hidden';
  setTimeout(() => { fadeOut.value = true; }, 2200);
  setTimeout(() => {
    loading.value = false;
    document.documentElement.style.overflow = '';
  }, 2700);
});

const dashboard = useDashboard();
dashboard.initial();
const blockchain = useBlockchain();
blockchain.randomSetupEndpoint();
const baseStore = useBaseStore();

const current = ref('');
const temp = ref('');
blockchain.$subscribe((m, s) => {
  if (current.value === s.chainName && temp.value != s.endpoint.address) {
    temp.value = s.endpoint.address;
    blockchain.initial();
  }
  if (current.value != s.chainName) {
    current.value = s.chainName;
    blockchain.randomSetupEndpoint();
  }
});

const sidebarShow = ref(false);
const sidebarOpen = ref(true);

const changeOpen = (index: Number) => {
  if (index === 0) {
    sidebarOpen.value = !sidebarOpen.value;
  }
};
const showDiscord = window.location.host.search('ping.pub') > -1;

function isNavGroup(nav: VerticalNavItems | any): nav is NavGroup {
  return (<NavGroup>nav).children !== undefined;
}
function isNavLink(nav: VerticalNavItems | any): nav is NavLink {
  return (<NavLink>nav).to !== undefined;
}
function isNavTitle(nav: VerticalNavItems | any): nav is NavSectionTitle {
  return (<NavSectionTitle>nav).heading !== undefined;
}
function selected(route: any, nav: NavLink) {
  const b =
    route.path === nav.to?.path || (route.path.startsWith(nav.to?.path) && nav.title.indexOf('dashboard') === -1);
  return b;
}
const blocktime = computed(() => {
  return dayjs(baseStore.latest?.block?.header?.time);
});

const behind = computed(() => {
  const current = dayjs().subtract(10, 'minute');
  return blocktime.value.isBefore(current);
});

dayjs();

const show_ad = computed(() => {
  return location.hostname.indexOf('ping.pub') > -1;
});
</script>

<template>
  <!-- Loading Screen -->
  <div v-if="loading" :style="{ opacity: fadeOut ? 0 : 1 }" style="
    position: fixed; inset: 0; z-index: 9999;
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    background: linear-gradient(135deg, #0a0a1a 0%, #0d0d2b 40%, #0a0a1a 100%);
    transition: opacity 0.5s ease;
  ">
    <!-- Border frame -->
    <div style="
      position: absolute; inset: 12px;
      border: 1.5px solid rgba(168,85,247,0.5);
      border-radius: 16px;
      box-shadow: 0 0 30px rgba(168,85,247,0.15), inset 0 0 30px rgba(168,85,247,0.05);
      pointer-events: none;
    "/>
    <!-- Logo container -->
    <div style="position: relative; width: 220px; height: 220px; display: flex; align-items: center; justify-content: center; margin-bottom: 8px;">
      <div class="ring2" style="position: absolute; width: 210px; height: 210px; border-radius: 50%; border: 1px solid rgba(168,85,247,0.35); top: 0; left: 0;"/>
      <div class="ring1" style="position: absolute; width: 160px; height: 160px; border-radius: 50%; border: 1.5px solid rgba(168,85,247,0.55); top: 30px; left: 30px;"/>
      <div class="coin-flip" style="width: 120px; height: 120px; border-radius: 50%; overflow: hidden; border: 3px solid #A855F7; box-shadow: 0 0 30px rgba(168,85,247,0.7), 0 0 60px rgba(168,85,247,0.3);">
        <img src="/logo.png" alt="alfadzc logo" style="width: 100%; height: 100%; object-fit: cover;" />
      </div>
    </div>
    <!-- Text -->
    <div class="fade-in-text" style="text-align: center;">
      <p style="font-size: 13px; letter-spacing: 0.15em; color: rgba(255,255,255,1); font-family: monospace; display: flex; align-items: center; justify-content: center; gap: 4px;">
        𝙿𝚕𝚎𝚊𝚜𝚎 𝚠𝚊𝚒𝚝
        <span class="dot1" style="display: inline-block;">•</span>
        <span class="dot2" style="display: inline-block;">•</span>
        <span class="dot3" style="display: inline-block;">•</span>
      </p>
    </div>
  </div>

  <!-- Main App -->
  <div class="bg-gray-100 dark:bg-[#171d30]">
    <div
      class="w-64 fixed z-50 left-0 top-0 bottom-0 overflow-auto bg-base-100 border-r border-gray-100 dark:border-gray-700"
      :class="{ block: sidebarShow, 'hidden xl:!block': !sidebarShow }"
    >
      <div class="flex justify-between mt-1 pl-4 py-4 mb-1">
        <RouterLink to="/" class="flex items-center">
          <div class="flex items-center">
            <div class="flex flex-col items-center">
              <img class="w-10 h-10 rounded-full" src="../../assets/logo.png" />
            </div>
            <span class="ml-3 text-2xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-[#a855f7] to-[#3b82f6] py-1">
              Alfadzc
            </span>
          </div>
        </RouterLink>
        <div class="pr-4 cursor-pointer xl:!hidden" @click="sidebarShow = false">
          <Icon icon="mdi-close" class="text-2xl" />
        </div>
      </div>
      <div v-for="(item, index) of blockchain.computedChainMenu" :key="index" class="px-2">
        <div
          v-if="isNavGroup(item)"
          :tabindex="index"
          class="collapse"
          :class="{
            'collapse-arrow': index > 0 && item?.children?.length > 0,
            'collapse-open': index === 0 && sidebarOpen,
            'collapse-close': index === 0 && !sidebarOpen,
          }"
        >
          <input v-if="index > 0" type="checkbox" class="cursor-pointer !h-10 block" @click="changeOpen(index)" />
          <div class="collapse-title !py-0 px-4 flex items-center cursor-pointer hover:bg-gray-100 dark:hover:bg-[#373f59]">
            <Icon
              v-if="item?.icon?.icon"
              :icon="item?.icon?.icon"
              class="text-xl mr-2"
              :class="{
                'text-yellow-500': item?.title === 'Favorite',
                'text-blue-500': item?.title !== 'Favorite',
              }"
            />
            <img v-if="item?.icon?.image" :src="item?.icon?.image" class="w-6 h-6 rounded-full mr-3" />
            <div class="text-base capitalize flex-1 text-gray-700 dark:text-gray-200 whitespace-nowrap">{{ item?.title }}</div>
            <div v-if="item?.badgeContent" class="mr-6 badge badge-sm text-white border-none" :class="item?.badgeClass">{{ item?.badgeContent }}</div>
          </div>
          <div class="collapse-content">
            <div v-for="(el, key) of item?.children" class="menu bg-base-100 w-full !p-0">
              <RouterLink
                v-if="isNavLink(el)"
                @click="sidebarShow = false"
                class="hover:bg-gray-100 dark:hover:bg-[#373f59] rounded cursor-pointer px-3 py-2 flex items-center"
                :class="{ '!bg-primary': selected($route, el) }"
                :to="el.to"
              >
                <Icon v-if="!el?.icon?.image" icon="mdi:chevron-right" class="mr-2 ml-3" :class="{ 'text-white': $route.path === el?.to?.path && item?.title !== 'Favorite' }" />
                <img v-if="el?.icon?.image" :src="el?.icon?.image" class="w-6 h-6 rounded-full mr-3 ml-4" :class="{ 'border border-gray-300 bg-white': selected($route, el) }" />
                <div class="text-base capitalize text-gray-500 dark:text-gray-300" :class="{ '!text-white': selected($route, el) }">
                  {{ item?.title === 'Favorite' ? el?.title : $t(el?.title) }}
                </div>
              </RouterLink>
            </div>
            <div v-if="index === 0 && dashboard.networkType === NetworkType.Testnet" class="menu bg-base-100 w-full !p-0">
              <RouterLink class="hover:bg-gray-100 dark:hover:bg-[#373f59] rounded cursor-pointer px-3 py-2 flex items-center" :to="`/${blockchain.chainName}/faucet`">
                <Icon icon="mdi:chevron-right" class="mr-2 ml-3"></Icon>
                <div class="text-base capitalize text-gray-500 dark:text-gray-300">Faucet</div>
                <div class="badge badge-sm text-white border-none badge-error ml-auto">New</div>
              </RouterLink>
            </div>
          </div>
        </div>

        <RouterLink
          v-if="isNavLink(item)"
          :to="item?.to"
          @click="sidebarShow = false"
          class="cursor-pointer rounded-lg px-4 flex items-center py-2 hover:bg-gray-100 dark:hover:bg-[#373f59]"
        >
          <Icon v-if="item?.icon?.icon" :icon="item?.icon?.icon" class="text-xl mr-2" :class="{ 'text-yellow-500': item?.title === 'Favorite', 'text-blue-500': item?.title !== 'Favorite' }" />
          <img v-if="item?.icon?.image" :src="item?.icon?.image" class="w-6 h-6 rounded-full mr-3 border border-blue-100" />
          <div class="text-base capitalize flex-1 text-gray-700 dark:text-gray-200 whitespace-nowrap">{{ item?.title }}</div>
          <div v-if="item?.badgeContent" class="badge badge-sm text-white border-none" :class="item?.badgeClass">{{ item?.badgeContent }}</div>
        </RouterLink>
        <div v-if="isNavTitle(item)" class="px-4 text-sm text-gray-400 pb-2 uppercase">{{ item?.heading }}</div>
      </div>
      <div class="px-2">
        <div class="px-4 text-sm pt-2 text-gray-400 pb-2 uppercase">Tools</div>
        <RouterLink to="/wallet/suggest" class="py-2 px-4 flex items-center cursor-pointer rounded-lg hover:bg-gray-100 dark:hover:bg-[#373f59]">
          <Icon icon="mdi:frequently-asked-questions" class="text-xl mr-2" />
          <div class="text-base capitalize flex-1 text-gray-600 dark:text-gray-200">Wallet Helper</div>
        </RouterLink>
        <div v-if="showDiscord" class="px-4 text-sm pt-2 text-gray-400 pb-2 uppercase">{{ $t('module.sponsors') }}</div>
        <Sponsors v-if="showDiscord" />
        <div class="px-4 text-sm pt-2 text-gray-400 pb-2 uppercase">{{ $t('module.links') }}</div>
        <a href="https://twitter.com/ping_pub" target="_blank" class="py-2 px-4 flex items-center cursor-pointer rounded-lg hover:bg-gray-100 dark:hover:bg-[#373f59]">
          <Icon icon="mdi:twitter" class="text-xl mr-2" />
          <div class="text-base capitalize flex-1 text-gray-600 dark:text-gray-200">Twitter</div>
        </a>
        <a v-if="showDiscord" href="https://discord.com/invite/CmjYVSr6GW" target="_blank" class="py-2 px-4 flex items-center rounded-lg cursor-pointer hover:bg-gray-100 dark:hover:bg-[#373f59]">
          <Icon icon="mdi:discord" class="text-xl mr-2" />
          <div class="text-base capitalize flex-1 text-gray-600 dark:text-gray-200">Discord</div>
        </a>
        <a href="https://github.com/ping-pub/explorer/discussions" target="_blank" class="py-2 px-4 flex items-center rounded-lg cursor-pointer hover:bg-gray-100 dark:hover:bg-[#373f59]">
          <Icon icon="mdi:frequently-asked-questions" class="text-xl mr-2" />
          <div class="text-base capitalize flex-1 text-gray-600 dark:text-gray-200">FAQ</div>
        </a>
      </div>
    </div>
    <div class="xl:!ml-64 px-3 pt-4">
      <div class="flex items-center py-3 bg-base-100 mb-4 rounded px-4 sticky top-0 z-10">
        <div class="text-2xl pr-3 cursor-pointer xl:!hidden" @click="sidebarShow = true">
          <Icon icon="mdi-menu" />
        </div>
        <ChainProfile />
        <div class="flex-1 w-0"></div>
        <NavBarI18n class="hidden md:!inline-block" />
        <NavbarThemeSwitcher class="!inline-block" />
        <NavbarSearch class="!inline-block" />
        <NavBarWallet />
      </div>

      <div style="min-height: calc(100vh - 180px)">
        <div v-if="behind" class="alert alert-error mb-4">
          <div class="flex gap-2">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="stroke-current flex-shrink-0 w-6 h-6">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
            </svg>
            <span>{{ $t('pages.out_of_sync') }} {{ blocktime.format() }} ({{ blocktime.fromNow() }})</span>
          </div>
        </div>
        <RouterView v-slot="{ Component }">
          <Transition mode="out-in">
            <div>
              <AdBanner v-if="show_ad" />
              <Component :is="Component" :class="{ '[&_h1]:!bg-clip-text [&_h1]:!text-transparent [&_h1]:!bg-gradient-to-r [&_h1]:!from-[#a855f7] [&_h1]:!to-[#3b82f6] [&_h1]:!font-bold [&_h1]:pb-2 [&_h1]:leading-relaxed [&_.text-4xl]:!bg-clip-text [&_.text-4xl]:!text-transparent [&_.text-4xl]:!bg-gradient-to-r [&_.text-4xl]:!from-[#a855f7] [&_.text-4xl]:!to-[#3b82f6] [&_.text-4xl]:pb-2 [&_.text-4xl]:leading-relaxed [&_svg]:!text-[#a855f7] [&_path]:!fill-[#a855f7]': $route.path === '/' }" />
            </div>
          </Transition>
        </RouterView>
      </div>

      <newFooter />
    </div>
  </div>
</template>

<style scoped>
@keyframes coinFlip {
  0%   { transform: rotateY(0deg); }
  100% { transform: rotateY(360deg); }
}
@keyframes pulse-ring {
  0%   { opacity: 0.6; transform: scale(0.85); }
  50%  { opacity: 0.2; transform: scale(1.1); }
  100% { opacity: 0.6; transform: scale(0.85); }
}
@keyframes pulse-ring2 {
  0%   { opacity: 0.4; transform: scale(0.75); }
  50%  { opacity: 0.1; transform: scale(1.2); }
  100% { opacity: 0.4; transform: scale(0.75); }
}
@keyframes dotBounce {
  0%, 80%, 100% { transform: translateY(0); opacity: 0.4; }
  40%           { transform: translateY(-6px); opacity: 1; }
}
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(10px); }
  to   { opacity: 1; transform: translateY(0); }
}
.coin-flip  { animation: coinFlip 1.2s linear infinite; transform-style: preserve-3d; }
.ring1      { animation: pulse-ring  2s ease-in-out infinite; }
.ring2      { animation: pulse-ring2 2s ease-in-out infinite 0.3s; }
.dot1       { animation: dotBounce 1.2s ease-in-out infinite 0s; }
.dot2       { animation: dotBounce 1.2s ease-in-out infinite 0.2s; }
.dot3       { animation: dotBounce 1.2s ease-in-out infinite 0.4s; }
.fade-in-text { animation: fadeInUp 0.8s ease forwards 0.4s; opacity: 0; }
</style>
