<template>
  <div class="app-container">
    <el-col :span="24">
      <el-col :span="16">
        <el-col :span="12">
          <el-card style="height: 350px; margin: 8px">
            <div id="world_trend" :style="{width: '100%', height: '330px'}" />
          </el-card>
        </el-col>
        <el-col :span="12">
          <el-card style="height: 350px; margin: 8px">
            <div id="china_trend" :style="{width: '100%', height: '330px'}" />
          </el-card>
        </el-col>
        <el-col :span="24">
          <el-card style="height: 400px; margin: 8px">
            <div id="china_line" :style="{width: '100%', height: '360px'}" />
          </el-card>
        </el-col>
      </el-col>
      <el-col :span="8">
        <el-card style="height: 250px; margin: 8px">
          <div id="top_confirm" :style="{width: '100%', height: '230px'}" />
        </el-card>
        <el-card style="height: 250px; margin: 8px">
          <div id="top_dead" :style="{width: '100%', height: '230px'}" />
        </el-card>
        <el-card style="height: 250px; margin: 8px">
          <div id="top_province" :style="{width: '100%', height: '230px'}" />
        </el-card>
      </el-col>
    </el-col>
  </div>
</template>

<script>
import axios from 'axios'
import echarts from 'echarts'
require('echarts/theme/macarons')

export default {
  data() {
    return {
      china_last_14: [],
      world_trend: null,
      china_trend: null,
      top_confirm: null,
      top_dead: null,
      top_province: null,
      china_line: null,
      main_province: null,
      china_scatter: [84484, 157, 79682, 4645],
      global_scatter: [4239558, 2475235, 1472340, 291983],
      global_confirm: [
        { value: 84461, name: '中国' },
        { value: 1370016, name: '美国' },
        { value: 242271, name: '俄罗斯' },
        { value: 221216, name: '意大利' },
        { value: 228691, name: '西班牙' },
        { value: 226463, name: '英国' },
        { value: 1866440, name: '其他' }
      ],
      china_top_pro: [
        { value: 68134, name: '湖北省' },
        { value: 1589, name: '广东省' },
        { value: 1276, name: '河南省' },
        { value: 1268, name: '浙江省' },
        { value: 12071, name: '其他' }
      ]
    }
  },
  created() {
  },
  mounted() {
    var that = this
    that.world_trend = echarts.init(document.getElementById('world_trend'), 'macarons')
    that.china_trend = echarts.init(document.getElementById('china_trend'), 'macarons')
    that.top_confirm = echarts.init(document.getElementById('top_confirm'), 'macarons')
    that.top_dead = echarts.init(document.getElementById('top_dead'), 'macarons')
    that.china_line = echarts.init(document.getElementById('china_line'), 'macarons')
    that.top_province = echarts.init(document.getElementById('top_province'), 'macarons')
    this.init_trend()
    this.fetch_china_data()
  },
  methods: {
    init_trend() {
      var that = this
      var search_date = '2020-05-17'
      that.world_trend.setOption({
        title: {
          text: '世界疫情仪表盘',
          subtext: search_date
        },
        grid: {
          left: '2%',
          right: '2%',
          bottom: '2%',
          containLabel: true
        },
        tooltip: {
          trigger: 'item'
        },
        xAxis: {
          type: 'category',
          data: ['累计确诊', '现存确诊', '累计治愈', '累计死亡']
        },
        yAxis: {
          type: 'value'
        },
        series: [{
          name: '世界疫情仪表盘',
          data: that.global_scatter,
          type: 'effectScatter',
          symbolSize: function(data) {
            return data / 150000
          },
          color: '#2a8416',
          label: {
            show: true,
            position: 'top'
          }
        }]
      })
      that.china_trend.setOption({
        title: {
          text: '中国疫情仪表盘',
          subtext: search_date
        },
        grid: {
          left: '2%',
          right: '2%',
          bottom: '2%',
          containLabel: true
        },
        tooltip: {
          trigger: 'item'
        },
        xAxis: {
          type: 'category',
          data: ['累计确诊', '现存确诊', '累计治愈', '累计死亡']
        },
        yAxis: {
          type: 'value'
        },
        series: [{
          name: '中国疫情仪表盘',
          data: that.china_scatter,
          type: 'effectScatter',
          symbolSize: function(data) {
            return data / 4000
          },
          color: '#1a20f1',
          label: {
            show: true,
            position: 'top'
          }
        }]
      })
      that.top_confirm.setOption({
        title: {
          text: 'Top累计确诊（世界）',
          subtext: search_date
        },
        grid: {
          left: '2%',
          right: '2%',
          bottom: '2%',
          containLabel: true
        },
        tooltip: {
          trigger: 'item',
          formatter: '{a} <br/>{b} : {c} ({d}%)'
        },
        series: [
          {
            name: '累计确诊',
            type: 'pie',
            radius: '60%',
            center: ['50%', '50%'],
            data: that.global_confirm,
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowOffsetX: 0,
                shadowColor: 'rgba(0, 0, 0, 0.5)'
              }
            }
          }
        ]
      })
      that.top_dead.setOption({
        title: {
          text: 'Top死亡（世界）',
          subtext: search_date
        },
        grid: {
          left: '2%',
          right: '2%',
          bottom: '2%',
          containLabel: true
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            crossStyle: {
              color: '#999'
            }
          }
        },
        yAxis: [
          {
            type: 'category',
            data: ['法国', '西班牙', '意大利', '英国', '美国'],
            axisPointer: {
              type: 'shadow'
            }
          }
        ],
        xAxis: [
          {
            type: 'value'
          },
          {
            type: 'value',
            axisLabel: {
              formatter: '{value} %'
            }
          }
        ],
        series: [
          {
            name: '累计死亡',
            type: 'bar',
            data: [26991, 27104, 30911, 32692, 82389],
            label: {
              show: true,
              position: 'insideRight'
            }
          },
          {
            name: '死亡率',
            type: 'line',
            xAxisIndex: 1,
            data: [19.24, 11.85, 13.97, 14.43, 6.01]
          }
        ]
      })
      that.top_province.setOption({
        title: {
          text: 'Top累计确诊（中国）',
          subtext: search_date
        },
        grid: {
          left: '2%',
          right: '2%',
          bottom: '2%',
          containLabel: true
        },
        tooltip: {
          trigger: 'item',
          formatter: '{a} <br/>{b}: {c} ({d}%)'
        },
        series: [{
          name: '数据对比',
          type: 'pie',
          radius: ['40%', '60%'],
          center: ['50%', '60%'],
          avoidLabelOverlap: true,
          label: {
            position: 'outer'
          },
          data: that.china_top_pro
        }]
      })
    },
    update_china_trend() {
      var that = this
      var days_data = []
      var last_confirm_data = []
      var last_current_data = []
      var last_cured_data = []
      var last_dead_data = []
      for (var i = 0, len = that.china_last_14.length; i < len; i++) {
        days_data[i] = that.china_last_14[i].day
        last_confirm_data[i] = that.china_last_14[i].confirm
        last_current_data[i] = that.china_last_14[i].current
        last_cured_data[i] = that.china_last_14[i].cured
        last_dead_data[i] = that.china_last_14[i].dead
      }
      that.china_line.setOption({
        title: {
          text: '14天中国疫情趋势'
        },
        xAxis: {
          data: days_data
        },
        yAxis: [{
          type: 'value'
        },
        {
          type: 'value',
          max: 10000
        }],
        grid: {
          left: '2%',
          right: '2%',
          bottom: '2%',
          containLabel: true
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross'
          }
        },
        legend: {
          data: ['累计确诊', '现存确诊', '累计治愈', '累计死亡']
        },
        series: [{
          name: '累计确诊',
          smooth: true,
          type: 'line',
          data: last_confirm_data,
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        },
        {
          name: '现存确诊',
          smooth: true,
          type: 'bar',
          yAxisIndex: 1,
          areaStyle: {},
          data: last_current_data,
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        },
        {
          name: '累计治愈',
          smooth: true,
          type: 'line',
          data: last_cured_data,
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        },
        {
          name: '累计死亡',
          smooth: true,
          type: 'bar',
          yAxisIndex: 1,
          data: last_dead_data,
          areaStyle: {},
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        }]
      })
    },
    fetch_china_data() {
      var that = this
      axios.get('/server/getChinaSeries/', {
        params: {
          day: '2020-05-11'
        }
      }).then(function(res) {
        that.china_last_14 = res.data
        that.update_china_trend()
      })
    }
  }
}

</script>

<style scoped>

</style>
