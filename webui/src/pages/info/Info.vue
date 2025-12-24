<template>
  <div style="font-size: 24px; font-weight: bold;">系统信息</div>
  <a-divider></a-divider>
  <div class="info">
    <a-descriptions
      title="版本信息"
      :column="{ xxl: 4, xl: 3, lg: 3, md: 3, sm: 2, xs: 1 }"
      >
      <a-descriptions-item label="主版本">{{ version.version }}</a-descriptions-item>
      <a-descriptions-item label="编译版本"><a @click="gotoVersion">{{ version.head }}</a></a-descriptions-item>
      <a-descriptions-item label="发布时间">{{ version.updateTime }}</a-descriptions-item>
      <a-descriptions-item label="更新信息">{{ version.commitInfo }}</a-descriptions-item>
    </a-descriptions>
    <a-divider></a-divider>
    <a-descriptions
      title="使用说明"
      :column="{ xxl: 4, xl: 3, lg: 3, md: 3, sm: 2, xs: 1 }"
      >
      <a-descriptions-item label="Wiki"><a @click="gotoWiki">Wiki</a></a-descriptions-item>
    </a-descriptions>
  </div>
</template>
<script>
export default {
  data () {
    return {
      version: {},
      runInfo: {}
    };
  },
  methods: {
    isMobile () {
      if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
        return true;
      } else {
        return false;
      }
    },
    async getRunInfo () {
      try {
        const res = await this.$api().setting.getRunInfo();
        this.runInfo = res.data;
      } catch (e) {
        await this.$message().error(e.message);
      }
    },
    async gotoWiki () {
      window.open('https://wiki.vertex-app.top');
    },
    async gotoVersion () {
      window.open('https://github.com/silencoo/vertex');
    }
  },
  async mounted () {
    this.getRunInfo();
    this.version = process.env.version;
  }
};
</script>
<style scoped>
.info {
  width: 100%;
  max-width: 1440px;
  margin: 0 auto;
}
</style>
