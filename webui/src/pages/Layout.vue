<template>
  <a-layout class="dashboard">
    <a-layout-sider
      v-if="!isMobile()"
      theme="light">
      <a-menu
        v-model:selectedKeys="selectedKeys"
        v-model:openKeys="openKeys"
        mode="inline"
        style="height: calc(var(--vh, 1vh) * 100); overflow: auto; overflow-x: hidden; "
      >
        <div class="logo" @click="gotoWiki">
          <div style="width: 32px; float: left;">
            <img src="/assets/images/logo.svg"/>
          </div>
          <span style="font-size: 28px; line-height: 32px; padding-left: 12px;">
            Vertex
          </span>
        </div>
        <template v-for="item of menu">
          <a-menu-item v-if="!item.hidden && !item.sub" :key="item.path" @click="goto(item.path)">
            <template #icon>
              <fa :icon="item.icon" style="width: 32px;"></fa>
            </template>
            {{ item.title }}
          </a-menu-item>
          <a-sub-menu v-if="!item.hidden && item.sub" :key="item.path">
            <template #icon>
              <fa :icon="item.icon" style="width: 32px;"></fa>
            </template>
            <template #title>
              {{ item.title }}
            </template>
            <template v-for="subItem of item.sub" :key="subItem.path">
              <a-menu-item v-if="!subItem.hidden" @click="goto(subItem.path)" :key="subItem.path">
                <template #icon>
                  <fa :icon="subItem.icon" style="width: 32px;"></fa>
                </template>
                {{ subItem.title }}
              </a-menu-item>
            </template>
          </a-sub-menu>
        </template>
      </a-menu>
    </a-layout-sider>
    <a-drawer v-if="isMobile()" v-model:visible="visible" :closable="false" placement="left" width="272">
      <a-menu
        v-model:selectedKeys="selectedKeys"
        v-model:openKeys="openKeys"
        mode="inline"
        style="width: 220px; font-size: 14px; min-height: calc(var(--vh, 1vh) * 100 - 48px);"
      >
        <div class="logo">
          <div style="width: 32px; float: left;">
            <img src="/assets/images/logo.svg"/>
          </div>
          <span style="font-size: 22px; line-height: 32px; padding-left: 12px;">
            Vertex
          </span>
        </div>
        <template v-for="item of menu">
          <a-menu-item v-if="!item.hidden && !item.sub" :key="item.path" @click="goto(item.path); visible = false;">
            <template #icon>
              <fa :icon="item.icon" style="width: 32px;"></fa>
            </template>
            {{ item.title }}
          </a-menu-item>
          <a-sub-menu v-if="!item.hidden && item.sub" :key="item.path">
            <template #icon>
              <fa :icon="item.icon" style="width: 32px;"></fa>
            </template>
            <template #title>
              {{ item.title }}
            </template>
            <template v-for="subItem of item.sub" :key="subItem.path">
              <a-menu-item v-if="!subItem.hidden" @click="goto(subItem.path); visible = false;" :key="subItem.path">
                <template #icon>
                  <fa :icon="subItem.icon" style="width: 32px;"></fa>
                </template>
                {{ subItem.title }}
              </a-menu-item>
            </template>
          </a-sub-menu>
        </template>
      </a-menu>
    </a-drawer>
    <a-layout>
      <a-layout-header v-if="isMobile()" class="mobile-header" theme="light">
        <fa v-if="!visible" :icon="['fas', 'bars']" @click="visible = !visible" class="menu-icon"/>
        <div class="mobile-logo-wrapper">
          <div class="mobile-logo-container">
            <img class="mobile-logo" src="/assets/images/logo.svg"/>
            <span class="mobile-title">
              Vertex
            </span>
          </div>
        </div>
      </a-layout-header>
      <div class="tabs-wrapper" v-if="!isMobile()">
        <a-tabs
          v-model:activeKey="activeTab"
          hide-add
          type="editable-card"
          @tabClick="onTabClick"
          @edit="onTabEdit"
          size="small"
          class="custom-tabs"
        >
          <a-tab-pane v-for="tab in tabs" :key="tab.path" :closable="tabs.length > 1">
            <template #tab>
              <a-dropdown :trigger="['contextmenu']">
                <span>{{ tab.title }}</span>
                <template #overlay>
                  <a-menu @click="handleTabMenuClick($event, tab.path)">
                    <a-menu-item key="close-others">关闭其它</a-menu-item>
                    <a-menu-item key="close-all" v-if="tabs.length > 1">关闭全部</a-menu-item>
                  </a-menu>
                </template>
              </a-dropdown>
            </template>
          </a-tab-pane>
        </a-tabs>
      </div>
      <a-layout-content
        :class="['layout-content', isMobile() ? 'mobile-content' : '']"
        :style="{ height: isMobile() ? 'calc(var(--vh, 1vh) * 100 - 64px)' : 'calc(var(--vh, 1vh) * 100 - 48px)' }">
        <router-view v-slot="{ Component }">
          <keep-alive>
            <component :is="Component" :key="$route.fullPath" />
          </keep-alive>
        </router-view>
      </a-layout-content>
    </a-layout>
  </a-layout>
</template>
<script>
export default {
  data () {
    return {
      selectedKeys: [],
      openKeys: [],
      visible: false,
      menu: [],
      tabs: [],
      activeTab: ''
    };
  },
  watch: {
    '$route' (to) {
      this.addTab(to);
    }
  },
  methods: {
    isMobile () {
      if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
        return true;
      } else {
        return false;
      }
    },
    async goto (to) {
      this.$goto(to, this.$router);
      setTimeout(() => {
        this.selectedKeys = [this.$route.path];
        const keys = [];
        for (const item of this.menu.filter(item => item.sub)) {
          if (this.$route.path.startsWith(item.path)) {
            keys.push(item.path);
          }
        }
        this.openKeys = keys;
      }, 100);
    },
    async gotoWiki () {
      window.open('https://wiki.vertex-app.top');
    },
    addTab (route) {
      if (route.path === '/') return;
      this.activeTab = route.path;
      if (!this.tabs.find(tab => tab.path === route.path)) {
        this.tabs.push({
          title: (route.meta.title || route.path).split(' - ')[0],
          path: route.path
        });
      }
    },
    onTabClick (path) {
      if (this.$route.path !== path) {
        this.$router.push(path);
      }
    },
    onTabEdit (targetPath, action) {
      if (action === 'remove') {
        const index = this.tabs.findIndex(tab => tab.path === targetPath);
        this.tabs = this.tabs.filter(tab => tab.path !== targetPath);
        if (this.activeTab === targetPath) {
          const nextTab = this.tabs[index] || this.tabs[index - 1];
          if (nextTab) {
            this.$router.push(nextTab.path);
          }
        }
      }
    },
    handleTabMenuClick ({ key }, path) {
      if (key === 'close-others') {
        this.tabs = this.tabs.filter(tab => tab.path === path);
        if (this.activeTab !== path) {
          this.$router.push(path);
        }
      } else if (key === 'close-all') {
        const firstTab = this.tabs[0];
        this.tabs = [firstTab];
        this.$router.push(firstTab.path);
      }
    }
  },
  async mounted () {
    this.addTab(this.$route);
    this.selectedKeys = [this.$route.path];
    try {
      const res = await this.$api().user.get();
      this.$message().success('欢迎回来');
      this.menu = res.data.menu;
      const keys = [];
      for (const item of this.menu.filter(item => item.sub)) {
        if (this.$route.path.startsWith(item.path)) {
          keys.push(item.path);
        }
      }
      this.openKeys = keys;
    } catch (e) {
      this.$message().error(e.message);
    }
    /*
    window.less.modifyVars({
      'component-background': 'purple',
      'layout-body-background': 'purple'
    }).then((res) => {
      console.log('成功');
    }).catch((res) => {
      console.log('错误');
    });
    */
  }
};
</script>
<style scoped lang="less">
.logo {
  height: 32px;
  margin: 24px auto;
  width: 144px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}

.logo img {
  width: 32px;
  filter: drop-shadow(0 2px 4px rgba(0,0,0,0.1));
}

.logo span {
  font-family: 'consolas';
  font-weight: bold;
  background: linear-gradient(135deg, #1890ff, #52c41a);
  background-clip: text;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.dashboard {
  height: calc(var(--vh, 1vh) * 100);
  background: #f0f2f5;
}

.layout-content {
  padding: 16px;
  overflow-y: auto;
  background: #fdfdfd;
  transition: all 0.3s;
}

.mobile-content {
  padding: 8px;
}

.mobile-header {
  padding: 0;
  background: #fff;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
  z-index: 10;
  height: 64px;
  display: flex;
  align-items: center;
}

.menu-icon {
  font-size: 18px;
  padding: 0 24px;
  cursor: pointer;
}

.mobile-logo-wrapper {
  flex: 1;
  display: flex;
  justify-content: center;
}

.mobile-logo-container {
  display: flex;
  align-items: center;
}

.mobile-logo {
  width: 24px;
  background: rgba(24, 144, 255, 0.1);
  border-radius: 4px;
  padding: 2px;
}

.mobile-title {
  font-size: 20px;
  padding-left: 8px;
  font-weight: bold;
}

.tabs-wrapper {
  background: #fff;
  padding: 4px 16px 0;
  border-bottom: 1px solid #f0f0f0;
  z-index: 9;
}

:deep(.custom-tabs) {
  .ant-tabs-nav {
    margin: 0;
    &::before {
      border-bottom: none;
    }
  }
  .ant-tabs-tab {
    background: transparent;
    border: none;
    transition: all 0.3s;
    margin-right: 2px;
    border-radius: 4px 4px 0 0;
    &:hover {
      background: rgba(0,0,0,0.02);
    }
  }
  .ant-tabs-tab-active {
    background: #fdfdfd !important;
    border: 1px solid #f0f0f0 !important;
    border-bottom-color: #fdfdfd !important;
  }
}

.ant-layout-sider {
  box-shadow: 4px 0 16px rgba(0,0,0,0.04);
  z-index: 11;
  background: #fdfdfd;
}

.ant-menu {
  border-right: none;
  background: transparent;
}

:deep(.ant-menu-item) {
  margin-top: 4px !important;
  margin-bottom: 4px !important;
  border-radius: 0 20px 20px 0 !important;
  width: calc(100% - 12px) !important;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1) !important;
  
  &:hover {
    color: #1890ff !important;
    background: rgba(24, 144, 255, 0.05) !important;
    padding-left: 28px !important;
  }
}

:deep(.ant-menu-item-selected) {
  background: linear-gradient(90deg, rgba(24, 144, 255, 0.1) 0%, rgba(24, 144, 255, 0.02) 100%) !important;
  color: #1890ff !important;
  font-weight: bold;
  &::after {
    border-right: 4px solid #1890ff !important;
    border-radius: 2px;
  }
}

:deep(.ant-menu-submenu-title) {
  margin-top: 4px !important;
  margin-bottom: 4px !important;
  border-radius: 0 20px 20px 0 !important;
  width: calc(100% - 12px) !important;
  
  &:hover {
    background: rgba(0, 0, 0, 0.02) !important;
  }
}
</style>
