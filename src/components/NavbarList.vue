<template>
  <router-link
    v-for="(row, i) in list"
    :key="i"
    :to="row.name === 'AI pretraga' ? '/ai-pretraga' : rootLink + convertToPath(row.name)"
    :class="{
      'router-link-active': $route.path.includes('razred') && row.name == 'Razred',
      blinking: row.blinking && blinking,
      'ai-button': row.name === 'AI pretraga',
      'nav-collapsed': navCollapsed,
    }"
    @click="rowClicked"
    v-tooltip.right="navCollapsed ? row.name : ''"
    v-wave
  >
    <div class="link-content">
      <span class="material-icons" :class="{ 'ai-icon': row.name === 'AI pretraga' }">
        {{ row.icon }}
      </span>
      <div class="text" :style="'--order: ' + (i + order)" v-if="!navCollapsed">
        {{ row.name }}
      </div>
      <span v-if="row.name === 'AI pretraga' && !navCollapsed" class="material-icons arrow-icon">
        arrow_forward
      </span>
    </div>
    <!-- Sparkle elements for AI button when not collapsed -->
    <template v-if="row.name === 'AI pretraga' && !navCollapsed">
      <span class="sparkle sparkle-1"></span>
      <span class="sparkle sparkle-2"></span>
      <span class="sparkle sparkle-3"></span>
      <span class="sparkle sparkle-4"></span>
      <span class="border-glow"></span>
    </template>
  </router-link>
</template>

<script lang="ts">
import { convertToPath } from "@/scripts/utils";
import { defineComponent, PropType } from "vue";
import { NavbarLink } from "./Navbar.vue";

export default defineComponent({
  name: "NavbarList",
  props: {
    list: {
      type: Array as PropType<NavbarLink[]>,
      required: true,
    },
    rootLink: {
      type: String,
      required: true,
    },
    order: {
      type: Number,
      required: true,
    },
    navCollapsed: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    return {
      blinking: true,
    };
  },
  methods: {
    rowClicked(e: MouseEvent) {
      if ((e.target as HTMLAnchorElement).classList.contains("blinking"))
        this.blinking = false;
    },
    convertToPath: (name: string) => convertToPath(name),
  },
});
</script>

<style lang="scss" scoped>
/* Wrapper for link content to help with centering */
.link-content {
  display: flex;
  align-items: center;
  width: 100%;
  height: 100%;
  position: relative;
  z-index: 2;
}

/* Animacija bljeskanja */
.blinking::after {
  content: "";
  position: absolute;
  right: 20px;
  border-radius: 50%;
  background: #ff7b00;
  width: 0.6rem;
  height: 0.6rem;
  animation: blink 3s infinite;
}

@keyframes blink {
  from,
  to {
    opacity: 0;
  }
  50% {
    opacity: 1;
  }
}

/* Stil aktivnog linka */
.router-link-active {
  background: $navbar-selected;
  color: $navbar-selected-text-color !important;

  .material-icons {
    text-shadow: 0 0 15px;
  }
}

/* Material icons styling */
.material-icons {
  display: flex;
  align-items: center;
  justify-content: center;
  padding-right: 15px;
}

/* Custom AI pretraga button */
.ai-button {
  display: flex;
  align-items: center;
  justify-content: center;
  background: transparent;
  border: 2px solid rgba(255, 255, 255, 0.3);
  color: white;
  font-weight: bold;
  padding: 12px 20px;
  border-radius: 12px;
  transition: all 0.3s ease-in-out;
  position: relative;
  margin: 15px auto;
  width: 90%;
  height: auto;
  overflow: hidden;
}

.ai-button .link-content {
  justify-content: space-between;
}

.border-glow {
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  border-radius: 12px;
  z-index: 1;
  pointer-events: none;
  background: transparent;
  border: 2px solid transparent;
  box-shadow: 0 0 8px rgba(0, 212, 255, 0.5);
  opacity: 0.6;
  animation: pulse 3s infinite;
}

@keyframes pulse {
  0% {
    opacity: 0.4;
    box-shadow: 0 0 5px rgba(0, 212, 255, 0.3);
  }
  50% {
    opacity: 0.8;
    box-shadow: 0 0 15px rgba(0, 212, 255, 0.7);
  }
  100% {
    opacity: 0.4;
    box-shadow: 0 0 5px rgba(0, 212, 255, 0.3);
  }
}

.sparkle {
  position: absolute;
  width: 4px;
  height: 4px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.8);
  pointer-events: none;
  z-index: 1;
  box-shadow: 0 0 10px 2px rgba(255, 255, 255, 0.5);
}

.sparkle-1 {
  top: 10%;
  left: 10%;
  animation: sparkle-move 4s infinite ease-in-out;
}

.sparkle-2 {
  top: 15%;
  right: 15%;
  animation: sparkle-move 5s infinite ease-in-out 1s;
}

.sparkle-3 {
  bottom: 15%;
  left: 20%;
  animation: sparkle-move 4.5s infinite ease-in-out 0.5s;
}

.sparkle-4 {
  bottom: 10%;
  right: 10%;
  animation: sparkle-move 3.5s infinite ease-in-out 1.5s;
}

@keyframes sparkle-move {
  0% {
    transform: translate(0, 0) scale(0.6);
    opacity: 0.2;
  }
  25% {
    transform: translate(10px, 5px) scale(1);
    opacity: 1;
  }
  50% {
    transform: translate(5px, 15px) scale(0.8);
    opacity: 0.6;
  }
  75% {
    transform: translate(-5px, 5px) scale(1);
    opacity: 0.8;
  }
  100% {
    transform: translate(0, 0) scale(0.6);
    opacity: 0.2;
  }
}

.ai-button::before {
  content: '';
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  border-radius: 12px;
  background: linear-gradient(90deg, 
    rgba(0, 212, 255, 0) 0%, 
    rgba(0, 212, 255, 0.5) 50%, 
    rgba(0, 212, 255, 0) 100%);
  z-index: 0;
  transform: translateX(-100%);
  animation: border-shine 6s infinite;
}

@keyframes border-shine {
  0% {
    transform: translateX(-100%);
    opacity: 0;
  }
  20% {
    opacity: 0.8;
  }
  40% {
    transform: translateX(100%);
    opacity: 0;
  }
  100% {
    transform: translateX(100%);
    opacity: 0;
  }
}

.ai-button:hover {
  background: rgba(255, 255, 255, 0.1);
  border-color: rgba(255, 255, 255, 0.6);
  box-shadow: 0px 0px 15px rgba(0, 212, 255, 0.5);
  transform: scale(1.05);
}

.ai-button .material-icons {
  font-size: 22px;
  transition: transform 0.2s ease-in-out;
}

.ai-button:hover .arrow-icon {
  transform: translateX(5px);
}

.nav-collapsed.ai-button {
  width: 100%;
  height: 50px;
  padding: 0;
  margin: 0;
  border-radius: 0;
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
}

.nav-collapsed.ai-button .link-content {
  justify-content: center;
}

.nav-collapsed.ai-button .material-icons {
  padding-right: 0;
  margin: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}

.nav-collapsed .text,
.nav-collapsed .arrow-icon {
  display: none;
}

.nav-collapsed .sparkle,
.nav-collapsed .border-glow,
.nav-collapsed::before {
  display: none;
}
</style>
