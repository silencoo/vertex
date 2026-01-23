<template>
  <div id="app">
    <div>
      <router-view v-slot="{ Component }">
        <transition name="fade-transform" mode="out-in">
          <component :is="Component" />
        </transition>
      </router-view>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {
  },
  data () {
    return {
    };
  },
  methods: {
  }
};
</script>
<style lang="less">
@import '../public/assets/styles/light.less';
@import '../public/assets/styles/dark.less' (prefers-color-scheme: dark);

@font-face {
  font-family: 'consolas';
  src: url('../public/assets/fonts/consolas.woff');
}

body {
  font-family: consolas, -apple-system, system-ui, BlinkMacSystemFont, Helvetica, Helvetica Neue, PingFang SC, 'Hiragino Sans GB',
    'Microsoft YaHei', '\u5fae\u8f6f\u96c5\u9ed1', '微软雅黑', Arial, sans-serif;
}

.container-form-mobile {
//  width: min(calc(100vw - 32px), 1440px);
  border-radius: 4px;
  padding: 12px;
  transition: all 0.5s;
}

.container-form-pc {
//  width: min(calc(100vw - 220px), 1440px);
  border-radius: 4px;
  padding: 12px;
  transition: all 0.5s;
}

img[lazy=loading] {
  object-fit: cover;
}

.ant-popover {
  z-index: 1055;
}

/*
html {
  overflow-y: scroll;
}

:root {
  overflow-y: auto;
  overflow-x: hidden;
}

:root body {
  position: absolute;
}

body {
  width: 100vw;
  overflow: hidden;
}
*/

/* Transitions */
.fade-transform-enter-active,
.fade-transform-leave-active {
  transition: all 0.3s cubic-bezier(0.55, 0, 0.1, 1);
}

.fade-transform-enter-from {
  opacity: 0;
  transform: translateX(-15px);
}

.fade-transform-leave-to {
  opacity: 0;
  transform: translateX(15px);
}

*::-webkit-scrollbar-thumb {
  border-radius: 10px;
  background-color: rgba(24, 144, 255, 0.3);
  border: 1px solid transparent;
  background-clip: padding-box;
}

*::-webkit-scrollbar-thumb:hover {
  background-color: rgba(24, 144, 255, 0.6);
}

*::-webkit-scrollbar {
  width : 6px;
  height: 6px;
}

*::-webkit-scrollbar-track {
  background: transparent;
}

</style>
