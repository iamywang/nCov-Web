<template>
  <div class="app-container">
    <el-col :span="24">
      <el-col :span="8">
        <el-card style="height: 300px; margin: 8px">
          <div id="world_trend" :style="{width: '100%', height: '280px'}" />
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card style="height: 300px; margin: 8px">
          <div id="top_confirm" :style="{width: '100%', height: '280px'}" />
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card style="height: 300px; margin: 8px">
          <div id="top_dead" :style="{width: '100%', height: '280px'}" />
        </el-card>
      </el-col>
    </el-col>
    <el-col :span="24">
      <el-card style="height: 730px; margin: 8px">
        <div id="global_map" :style="{width: '100%', height: '700px'}" />
      </el-card>
    </el-col>
  </div>
</template>

<script>
import axios from 'axios'
import echarts from 'echarts'
require('echarts/theme/macarons')
require('echarts/map/js/world')

export default {
  data() {
    return {
      world_trend: null,
      top_confirm: null,
      top_dead: null,
      global_map: null,
      confirm_list: [],
      global_dashboard: [4501097, 2592156, 1600791, 308150],
      global_top_confirm: [
        { value: 1442824, name: '美国' },
        { value: 262843, name: '俄罗斯' },
        { value: 236711, name: '英国' },
        { value: 230183, name: '西班牙' },
        { value: 223885, name: '意大利' },
        { value: 2104651, name: '其他' }
      ]
    }
  },
  mounted() {
    var that = this
    that.world_trend = echarts.init(document.getElementById('world_trend'), 'macarons')
    that.top_confirm = echarts.init(document.getElementById('top_confirm'), 'macarons')
    that.top_dead = echarts.init(document.getElementById('top_dead'), 'macarons')
    that.global_map = echarts.init(document.getElementById('global_map'), 'macarons')
    that.init_trend()
    that.fetchList()
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
          data: that.global_dashboard,
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
      that.top_confirm.setOption({
        title: {
          text: 'Top累计确诊',
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
            radius: ['40%', '60%'],
            center: ['50%', '50%'],
            data: that.global_top_confirm,
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
          text: 'Top累计死亡',
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
        xAxis: [
          {
            type: 'category',
            data: ['美国', '英国', '意大利', '西班牙', '法国', '巴西'],
            axisPointer: {
              type: 'shadow'
            }
          }
        ],
        yAxis: [
          {
            type: 'value'
          },
          {
            type: 'value',
            max: 20,
            axisLabel: {
              formatter: '{value} %'
            }
          }
        ],
        series: [
          {
            name: '累计死亡',
            type: 'bar',
            data: [87559, 33998, 31610, 27459, 27425, 14929],
            label: {
              show: true,
              position: 'top'
            },
            color: 'rgb(172,115,29)'
          },
          {
            name: '死亡率',
            type: 'line',
            yAxisIndex: 1,
            data: [6.06, 14.36, 14.11, 11.92, 19.40, 6.80],
            color: 'rgb(151,29,172)'
          }
        ]
      })
    },
    fetchList() {
      var that = this
      axios.get('/server/getGlobalMap/', {
        params: {
          key: 'confirm'
        }
      }).then(function(res) {
        that.confirm_list = res.data
        axios.get('/server/getGlobalMap/', {
          params: {
            key: 'current'
          }
        }).then(function(res2) {
          that.current_list = res2.data
          that.updateCharts()
        })
      })
    },
    updateCharts() {
      var that = this
      var search_date = '2020-05-17'
      that.global_map.setOption({
        title: {
          text: '累计确诊疫情地图',
          subtext: search_date
        },
        tooltip: {
          formatter: function(params, ticket, callback) {
            return params.seriesName + '<br />' + params.name + '：' + params.value
          }
        },
        visualMap: {
          min: 0,
          max: 200000,
          left: 'left',
          top: 'bottom',
          text: ['高', '低'],
          inRange: {
            color: ['#f5f5f5', '#ff5050', '#800000']
          },
          show: true
        },
        geo: {
          map: 'world',
          roam: true,
          zoom: 1,
          label: {
            show: true
          },
          itemStyle: {
            normal: {
              borderColor: 'rgba(0, 0, 0, 0.2)'
            }
          }
        },
        series: [
          {
            name: '累计确诊',
            type: 'map',
            geoIndex: 0,
            data: that.confirm_list
          }
        ]
      })
    }
  }
}

</script>

<style scoped>

</style>
