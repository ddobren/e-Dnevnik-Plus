<template>
  <div v-if="ad" id="carousel" ref="carousel" @mouseleave="resetInterval">
    <a :href="ad.url" target="_blank" class="ad-banner" @click="adClicked(ad!)">
      <transition :name="transitionTo">
        <img :src="ad.images.banner" class="banner" :key="adIdx" ref="banner" />
      </transition>
      <transition :name="navCollapsed ? 'left' : 'right'">
        <img v-if="navCollapsed" class="ad-logo abs-cover" :src="ad.images.logo" alt="Oglas" />
      </transition>
    </a>
    <template v-if="ads.length > 1">
      <div @click="slide('left')" class="control left">
        <span class="material-icons text-white"> arrow_back_ios </span>
      </div>
      <div @click="slide('right')" class="control right">
        <span class="material-icons text-white"> arrow_forward_ios </span>
      </div>
    </template>
  </div>
</template>

<script lang="ts">
import { Ad } from "@/store/state";
import { defineComponent, PropType } from "vue";

export default defineComponent({
  name: "NavbarAds",
  props: {
    ads: {
      type: Array as PropType<Ad[]>,
      required: true,
    },
    navCollapsed: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    return {
      transitionTo: "left",
      adIdx: 0,
      intervalId: 0,
      viewedAds: [] as string[],
    };
  },
  mounted() {
    this.resetInterval();
  },
  methods: {
    resetInterval() {
      clearInterval(this.intervalId);
      this.intervalId = setInterval(this.autoSlide, 15000);
    },
    autoSlide() {
      const carousel = this.$refs.carousel as HTMLElement | undefined;
      if (carousel && !carousel.matches(":hover")) this.slide("right");
    },
    slide(direction: string) {
      if ((this.$refs.banner as HTMLElement).className != "banner") return;
      if (direction == "left") {
        this.adIdx = this.adIdx > 0 ? this.adIdx - 1 : this.ads.length - 1;
      } else {
        this.adIdx = this.adIdx < this.ads.length - 1 ? this.adIdx + 1 : 0;
      }
      this.transitionTo = direction;
    },
    adClicked(ad: Ad) {
      chrome.runtime.sendMessage({
        name: "SEND_ANALYTICS_EVENT",
        params: {
          name: "click_ad",
          id: "ad-navbar",
          ad_id: ad.id,
        },
      });
    },
  },
  computed: {
    ad(): Ad | undefined {
      return this.ads[this.adIdx];
    },
  },
  watch: {
    ad: {
      handler(newValue) {
        if (!newValue || this.viewedAds.includes(newValue.id)) return;
        this.viewedAds.push(newValue.id);
        chrome.runtime.sendMessage({
          name: "SEND_ANALYTICS_EVENT",
          params: {
            name: "view_ad",
            id: "ad-navbar",
            ad_id: newValue.id,
          },
        });
      },
      immediate: true,
    },
  },
});
</script>

<style lang="scss" scoped>
#carousel {
  /* zasada sakrivamo ad u navbaru */
  display: none !important;

  position: relative;

  &:hover .control {
    opacity: 0.4;
  }
}

.ad-banner {
  position: relative;
  padding: 0 !important;
  width: 285px !important;
  height: 150px !important;

  img {
    height: 100%;
    transition: 1s;
  }
}

.ad-logo {
  transition: 500ms !important;
  z-index: 1;
}

.nav-collapsed .ad-banner {
  width: 100% !important;
}

.control {
  display: flex;
  position: absolute;
  top: 0;
  width: 30px;
  height: 100%;
  background-color: #000000;
  justify-content: center;
  align-items: center;
  opacity: 0;
  transition: opacity 150ms;

  &:hover {
    cursor: pointer;
    opacity: 0.6 !important;
  }

  .material-icons {
    color: white;
  }
}

.left {
  left: 0;

  span {
    transform: translateX(3px);
  }
}

.right {
  right: 0;
}

.left-enter-active,
.right-enter-active {
  position: absolute;
}

.left-enter-from,
.right-leave-to {
  transform: translateX(-100%);
}

.left-leave-to,
.right-enter-from {
  transform: translateX(100%);
}
</style>
