<template>
  <div class="index">
    <a-row type="flex" justify="center" align="middle" style="min-height: 100%;">
      <a-col :span="isMobile() ? 24 : 24">
        <div class="stats-container">
          <div class="glass-card stat-card blue">
            <div class="stat-content">
              <div class="stat-label">今日上传</div>
              <div class="stat-value">{{$formatSize(runInfo.uploadedToday)}}</div>
              <div class="stat-sub">Today Upload</div>
            </div>
          </div>
          <div class="glass-card stat-card cyan">
            <div class="stat-content">
              <div class="stat-label">今日下载</div>
              <div class="stat-value">{{$formatSize(runInfo.downloadedToday)}}</div>
              <div class="stat-sub">Today Download</div>
            </div>
          </div>
          <div class="glass-card stat-card green">
            <div class="stat-content">
              <div class="stat-label">今日添加</div>
              <div class="stat-value">{{runInfo.addCountToday}}</div>
              <div class="stat-sub">Today Accept</div>
            </div>
          </div>
          <div class="glass-card stat-card red">
            <div class="stat-content">
              <div class="stat-label">今日拒绝</div>
              <div class="stat-value">{{runInfo.rejectCountToday}}</div>
              <div class="stat-sub">Today Reject</div>
            </div>
          </div>
        </div>

        <div class="stats-container">
          <div class="glass-card info-card">
            <div class="stat-label">累计上传</div>
            <div class="stat-value small">{{$formatSize(runInfo.uploaded)}}</div>
          </div>
          <div class="glass-card info-card">
            <div class="stat-label">累计下载</div>
            <div class="stat-value small">{{$formatSize(runInfo.downloaded)}}</div>
          </div>
          <div class="glass-card info-card">
            <div class="stat-label">累计添加</div>
            <div class="stat-value small">{{runInfo.addCount}}</div>
          </div>
          <div class="glass-card info-card">
            <div class="stat-label">累计拒绝</div>
            <div class="stat-value small">{{runInfo.rejectCount}}</div>
          </div>
        </div>
        <!--
        <div style="margin: 24px auto; text-align: center; max-width: 1440px;">
          <div :class="`data-rect-3-${isMobile() ? 'mobile': 'pc'}`" style="background: #eff;">
          </div>
        </div>
        -->
        <div
          class="clients-grid"
          v-if="runInfo.dashboardContent.filter(item => item === 'downloader')[0]"
          >
          <template v-for="downloader in downloaders" :key="downloader.id">
            <div
              @click="gotoClient(`/proxy/client/${downloader.id}/`)"
              class="glass-card client-card clickable"
            >
              <div class="client-header">
                <div class="client-alias">{{ downloader.alias }}</div>
                <fa :icon="['fas', 'cloud']" class="client-icon"/>
              </div>
              <div class="client-data">
                <div class="data-item">
                  <span class="label">累计数据:</span>
                  <span class="value">{{ $formatSize(downloader.allTimeUpload) }} ↑ / {{$formatSize(downloader.allTimeDownload)}} ↓</span>
                </div>
                <div class="data-item speed">
                  <span class="value">{{ $formatSize(downloader.uploadSpeed) }}/s ↑</span>
                  <span class="value">{{ $formatSize(downloader.downloadSpeed) }}/s ↓</span>
                </div>
              </div>
            </div>
          </template>
        </div>
        <div
          style="margin: 24px auto; text-align: center; max-width: 1440px;"
          v-if="runInfo.dashboardContent.filter(item => item === 'server')[0]"
          >
          <template v-for="(server, index) in servers" :key="server.id">
            <div
              v-if="index === 0"
              class="data-rect-2 highlight-4"
              :style="servers.length === 1 ? `width: ${isMobile() ? '336px' : '688px'}` : ''">
              <!--
              <div style="position: absolute; left: 0; top: 0; width: 100%; height: 100%;">
                <v-chart :option="server.speedChart"/>
              </div>
              -->
              <div style="font-size: 14px; font-weight: bold; color: #fff; padding: 16px 16px;">
                <div>{{ server.alias }}</div>
                <div style="margin: initial; font-size: 12px;"></div>
                <div style="margin: initial; font-size: 16px;">{{ $formatSize(server.netSpeed.upload) }}/s ↑ / {{$formatSize(server.netSpeed.download)}}/s ↓</div>
              </div>
            </div>
            <div
              v-if="index !== 0"
              class="data-rect-2"
              :style="servers.length === index + 1 && servers.length % 2 === 1 ? `background: #eff; width: ${isMobile() ? '336px' : '688px'}` : 'background: #eff;'">
              <!--
              <div style="position: absolute; left: 0; top: 0; width: 100%; height: 100%;">
                <v-chart :option="server.speedChart" :init-options="{renderer: 'svg'}"/>
              </div>
              -->
              <div style="font-size: 14px; font-weight: bold; padding: 16px 16px;">
                <div>{{ server.alias }}</div>
                <div style="margin: initial; font-size: 12px;"></div>
                <div style="margin: initial; font-size: 16px;">{{ $formatSize(server.netSpeed.upload) }}/s ↑ / {{$formatSize(server.netSpeed.download)}}/s ↓</div>
              </div>
            </div>
          </template>
        </div>
        <div
          style="margin: 24px auto; text-align: center; max-width: 1440px;"
          v-if="runInfo.dashboardContent.filter(item => item === 'tracker')[0]"
          >
          <div :class="`data-rect-3-${isMobile() ? 'mobile': 'pc'}`" style="background: #eff; height: 400px;">
            <v-chart :option="trackerChart" autoresize/>
          </div>
        </div>
      </a-col>
      <!--
      <a-col :span="isMobile() ? 24 : 6">
        <div style="margin: 24px auto; width: fit-content; text-align: center;">
          <div style="background: #fff; width: 344px; height: 200px;">
            <v-chart :option="torrents" class="torrent-chart" style="height: 200px;" autoresize></v-chart>
          </div>
        </div>
        <div style="margin: 24px auto; width: fit-content; text-align: center;">
          <div style="background: #fff; width: 344px; height: 344px;">
            <v-chart :option="torrents" class="torrent-chart" autoresize></v-chart>
          </div>
        </div>
        <div style="margin: 24px auto; width: fit-content; text-align: center;">
          <div style="background: #fff; width: 240px; height: 240px;">
            <v-chart :option="trackerFlow" class="torrent-chart" style="height: 240px;" autoresize></v-chart>
          </div>
        </div>
      </a-col>
      -->
    </a-row>
  </div>
</template>
<script>
export default {
  data () {
    return {
      trackerChart: {
        title: {
          text: 'Tracker 速度',
          left: 'center',
          textStyle: {
            fontFamily: 'consolas'
          }
        },
        grid: {
          top: 20,
          left: this.isMobile() ? 0 : 90,
          right: 0,
          bottom: 90
        },
        legend: {
          show: false
        },
        textStyle: {
          fontFamily: 'consolas'
        },
        dataZoom: [
          {
            type: 'inside',
            start: 0,
            end: 100
          },
          {
            start: 0,
            end: 100
          }
        ],
        tooltip: {
          trigger: 'axis',
          position: function (pos, params, dom, rect, size) {
            const obj = { top: 60 };
            obj[['left', 'right'][+(pos[0] < size.viewSize[0] / 2)]] = 5;
            return obj;
          },
          formatter: (params) => {
            let str = params[0].axisValue + '</br>';
            params = params.sort((a, b) => b.value - a.value).filter(item => item.value);
            for (const param of params) {
              const size = this.$formatSize(param.value) + '/s';
              str += `${param.seriesName.slice(0, 20)}: ${'&nbsp;'.repeat(40 - size.length - param.seriesName.slice(0, 20).length || 1)}${size}<br>`;
            }
            return str;
          }
        },
        xAxis: {
          type: 'category',
          data: []
        },
        yAxis: {
          type: 'value',
          axisLabel: {
            show: !this.isMobile(),
            formatter: item => this.$formatSize(item) + '/s'
          }
        },
        graphic: [
          {
            type: 'image',
            id: 'logo',
            right: 20,
            top: 20,
            z: -1,
            bounding: 'raw',
            origin: [125, 125],
            style: {
              image: '/assets/images/logo.svg',
              width: 64,
              height: 64,
              opacity: 0.8
            }
          }
        ],
        series: []
      },
      speedChart: {
        grid: {
          left: 0,
          right: 0,
          top: 0,
          bottom: 0,
          z: 0
        },
        xAxis: {
          type: 'category',
          show: false,
          boundaryGap: false,
          data: []
        },
        yAxis: {
          type: 'value',
          show: false
        },
        series: [
          {
            data: [],
            type: 'line',
            symbol: 'none',
            smooth: true,
            areaStyle: {
              opacity: 0.2,
              color: '#BEC23F'
            },
            lineStyle: {
              opacity: 0,
              color: '#BEC23F'
            }
          }, {
            data: [],
            type: 'line',
            symbol: 'none',
            smooth: true,
            areaStyle: {
              opacity: 0,
              color: '#C46243'
            },
            lineStyle: {
              opacity: 0,
              color: '#C46243'
            }
          }
        ]
      },
      runInfo: {
        dashboardContent: []
      },
      trackerInfo: {},
      servers: [],
      downloaders: [],
      loading: true
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
    async listTrackerHistory () {
      try {
        const res = await this.$api().setting.getTrackerFlowHistory();
        this.trackerInfo = res;
        this.loadTracker();
      } catch (e) {
        await this.$message().error(e.message);
      }
    },
    async getRunInfo () {
      try {
        const res = await this.$api().setting.getRunInfo();
        this.runInfo = res.data;
        for (const error of this.runInfo.errors.reverse()) {
          await this.$notification().error({
            message: '存在错误信息, 请检查日志',
            description: error.map(item => {
              if (typeof item === 'object') {
                return item.message || item.code || item.description;
              }
              return item;
            }).join(', '),
            duration: 0
          });
        }
      } catch (e) {
        await this.$message().error(e.message);
      }
    },
    async listDownloader () {
      try {
        const res = await this.$api().downloader.listMainInfo();
        this.downloaders = res.data
          .sort((a, b) => a.alias.localeCompare(b.alias))
          .map(item => ({
            ...item,
            speedChart: JSON.parse(JSON.stringify(this.speedChart))
          }));
      } catch (e) {
        await this.$message().error(e.message);
      }
    },
    async listDownloaderInfo () {
      try {
        const res = await this.$api().downloader.listMainInfo();
        for (const downloader of this.downloaders) {
          const upload = res.data.filter(item => item.id === downloader.id)[0]?.uploadSpeed || 0;
          const download = res.data.filter(item => item.id === downloader.id)[0]?.downloadSpeed || 0;
          downloader.uploadSpeed = upload;
          downloader.downloadSpeed = download;
          // downloader.speedChart.xAxis.data.push('');
          // downloader.speedChart.series[0].data.push(upload);
          // downloader.speedChart.series[1].data.push(download);
        }
      } catch (e) {
        await this.$message().error(e.message);
      }
    },
    async listServer () {
      try {
        const res = await this.$api().server.list();
        this.servers = res.data
          .sort((a, b) => a.alias.localeCompare(b.alias))
          .map(item => (
            {
              ...item,
              netSpeed: {
                upload: 0,
                download: 0
              },
              speedChart: JSON.parse(JSON.stringify(this.speedChart))
            }));
      } catch (e) {
        await this.$message().error(e.message);
      }
    },
    async getNetSpeed () {
      try {
        this.netSpeed = (await this.$api().server.netSpeed()).data;
        for (const server of this.servers) {
          const upload = this.netSpeed[server.id]?.sort((a, b) => b.txBytes - a.txBytes)[0].txBytes || 0;
          const download = this.netSpeed[server.id]?.sort((a, b) => b.txBytes - a.txBytes)[0].rxBytes || 0;
          server.netSpeed = {
            upload,
            download
          };
          server.speedChart.xAxis.data.push('');
          server.speedChart.series[0].data.push(upload);
          // server.speedChart.series[1].data.push(download);
        }
      } catch (e) {
        await this.$message().error(e.message);
      }
    },
    loadTracker () {
      const recordList = this.trackerInfo.data.trackers;
      const template = {
        name: '',
        type: 'line',
        data: [],
        symbol: 'none',
        sampling: 'lttb',
        areaStyle: {
          opacity: 0.2
        },
        lineStyle: {
          opacity: 0.7
        },
        smooth: true
      };
      this.trackerChart.series = [];
      const dateSet = this.trackerInfo.data.timeGroup;
      for (const _tracker of Object.keys(recordList)) {
        const trackerRecord = recordList[_tracker];
        const tracker = { ...template };
        tracker.data = Object.keys(trackerRecord).map(i => Math.max(trackerRecord[i].upload, 0));
        tracker.name = _tracker;
        this.trackerChart.series.push(tracker);
      }
      if (this.trackerChart.series[0]) {
        const total = [];
        for (const [i] of this.trackerChart.series[0].data.entries()) {
          for (const series of this.trackerChart.series) {
            if (total[i]) {
              total[i] += Math.max(series.data[i], 0);
            } else {
              total[i] = Math.max(series.data[i], 0);
            }
          }
        }
        const t = { ...template };
        t.name = 'Total';
        t.data = total;
        this.trackerChart.series.push(t);
      }
      this.trackerChart.xAxis.data = dateSet.map(i => this.$moment(i * 1000).format('YYYY-MM-DD HH:mm'));
    },
    async gotoClient (url) {
      window.open(url);
    }
  },
  async mounted () {
    await this.getRunInfo();
    const downloader = !!this.runInfo.dashboardContent.filter(item => item === 'downloader')[0];
    const server = !!this.runInfo.dashboardContent.filter(item => item === 'server')[0];
    const tracker = !!this.runInfo.dashboardContent.filter(item => item === 'tracker')[0];
    if (downloader) {
      this.listDownloader();
      this.listDownloaderInfo();
    }
    if (server) {
      this.listServer();
      this.getNetSpeed();
    }
    if (tracker) {
      this.listTrackerHistory();
    }
    this.interval = setInterval(() => {
      if (downloader) {
        this.listDownloaderInfo();
      }
      if (server) {
        this.getNetSpeed();
      }
    }, 3000);
  },
  beforeUnmount () {
    clearInterval(this.interval);
  }
};
</script>
<style scoped lang="less">
.index {
  padding-bottom: 48px;
}

.stats-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 16px;
  margin: 24px auto;
  max-width: 1440px;
}

.glass-card {
  background: #ffffff;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.05);
  padding: 20px;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.glass-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.08);
}

.stat-card {
  width: 220px;
  height: 120px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  color: #fff;
}

.stat-card.blue { background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%); }
.stat-card.cyan { background: linear-gradient(135deg, #00cdac 0%, #8ddad5 100%); }
.stat-card.green { background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%); }
.stat-card.red { background: linear-gradient(135deg, #ff0844 0%, #ffb199 100%); }

.stat-label {
  font-size: 14px;
  font-weight: 600;
  margin-bottom: 4px;
}

.stat-value {
  font-size: 24px;
  font-weight: 700;
  line-height: 1.2;
}

.stat-value.small {
  font-size: 18px;
}

.stat-sub {
  font-size: 10px;
  opacity: 0.8;
  text-transform: uppercase;
  margin-top: 4px;
}

.info-card {
  width: 220px;
  height: 80px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  background: rgba(255, 255, 255, 0.8);
  color: #333;
}

.clients-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(450px, 1fr));
  gap: 16px;
  margin: 24px auto;
  max-width: 1440px;
}

.client-card {
  padding: 24px;
}

.client-card.clickable {
  cursor: pointer;
}

.client-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.client-alias {
  font-size: 18px;
  font-weight: bold;
  color: #1890ff;
}

.client-icon {
  font-size: 20px;
  color: #bfbfbf;
}

.client-data {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.data-item {
  display: flex;
  justify-content: space-between;
  font-size: 13px;
}

.data-item .label {
  color: #8c8c8c;
}

.data-item .value {
  font-weight: 500;
}

.data-item.speed {
  margin-top: 8px;
  padding-top: 8px;
  border-top: 1px dashed #eee;
}

.data-item.speed .value {
  color: #52c41a;
  font-size: 16px;
  font-weight: bold;
}

@media screen and (max-width: 768px) {
  .clients-grid {
    grid-template-columns: 1fr;
  }
  .stat-card, .info-card {
    width: 100%;
    margin: 0 16px;
  }
}

</style>
