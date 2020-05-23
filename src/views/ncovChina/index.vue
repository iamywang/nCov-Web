<template>
  <div class="app-container">
    <el-col :span="24">
      <el-col :span="8">
        <el-card style="height: 300px; margin: 8px">
          <div id="china_trend" :style="{width: '100%', height: '280px'}" />
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
      <el-col :span="12">
        <el-card style="height: 450px; margin: 8px">
          <div id="china_map" :style="{width: '100%', height: '420px'}" />
        </el-card>
      </el-col>
      <el-col :span="12">
        <el-card style="height: 450px; margin: 8px">
          <div id="china_current_map" :style="{width: '100%', height: '420px'}" />
        </el-card>
      </el-col>
    </el-col>
    <el-table :data="china_list" element-loading-text="正在查询" stripe border fit highlight-current-row>
      <el-table-column align="center" type="index" />
      <el-table-column align="center" label="日期" prop="day" />
      <el-table-column align="center" label="省/市" prop="place" />
      <el-table-column align="center" label="累计确诊" prop="confirm" />
      <el-table-column align="center" label="现存确诊" prop="current" />
      <el-table-column align="center" label="累计治愈" prop="cured" />
      <el-table-column align="center" label="累计死亡" prop="dead" />
    </el-table>
  </div>
</template>

<script>
import axios from 'axios'
import echarts from 'echarts'
require('echarts/theme/macarons')
require('echarts/map/js/china')

export default {
  data() {
    return {
      china_trend: null,
      top_confirm: null,
      top_dead: null,
      china_map: null,
      china_current_map: null,
      confirm_list: [],
      current_list: [],
      china_list: [],
      china_dashboard: [84522, 141, 79736, 4645],
      china_top_confirm: [
        { value: 44, name: '台湾' },
        { value: 26, name: '香港' },
        { value: 29, name: '吉林' },
        { value: 12, name: '内蒙古' },
        { value: 17, name: '上海' },
        { value: 7, name: '北京' },
        { value: 22, name: '其他' }
      ]
    }
  },
  mounted() {
    var that = this
    that.china_trend = echarts.init(document.getElementById('china_trend'), 'macarons')
    that.top_confirm = echarts.init(document.getElementById('top_confirm'), 'macarons')
    that.top_dead = echarts.init(document.getElementById('top_dead'), 'macarons')
    that.china_map = echarts.init(document.getElementById('china_map'), 'macarons')
    that.china_current_map = echarts.init(document.getElementById('china_current_map'), 'macarons')
    that.init_trend()
    that.fetchList()
  },
  methods: {
    init_trend() {
      var that = this
      var search_date = '2020-05-22'
      that.china_trend.setOption({
        title: {
          text: '中国疫情数据总览',
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
          name: '中国疫情数据总览',
          data: that.china_dashboard,
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
        title: [{
          text: 'Top现存确诊',
          subtext: search_date
        },
        {
          text: '{name|现存确诊' + '}\n{val|' + that.china_dashboard[1] + '}',
          top: 'center',
          left: 'center',
          textStyle: {
            rich: {
              name: {
                fontSize: 12,
                color: '#999999',
                padding: [10, 0]
              },
              val: {
                fontSize: 20,
                color: '#000000'
              }
            }
          }
        }],
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
            name: '现存确诊',
            type: 'pie',
            radius: ['40%', '60%'],
            center: ['50%', '53%'],
            data: that.china_top_confirm,
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
            data: ['湖北', '河南', '黑龙江', '北京', '广东', '山东', '上海'],
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
            max: 7,
            axisLabel: {
              formatter: '{value} %'
            }
          }
        ],
        series: [
          {
            name: '累计死亡',
            type: 'bar',
            data: [4512, 22, 13, 9, 8, 7, 7],
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
            data: [6.62, 1.72, 1.38, 1.52, 0.50, 0.89, 1.06],
            color: 'rgb(151,29,172)'
          }
        ]
      })
    },
    fetchList() {
      var that = this
      axios.get('/server/getChinaMap/', {
        params: {
          key: 'confirm',
          day: 'test'
        }
      }).then(function(res) {
        that.confirm_list = res.data
        axios.get('/server/getChinaMap/', {
          params: {
            key: 'current',
            day: 'test'
          }
        }).then(function(res2) {
          that.current_list = res2.data
          that.updateCharts()
        })
      })
      axios.get('/server/getChinaMapList/', {
        params: {
          key: 'all'
        }
      }).then(function(res) {
        that.china_list = res.data
      })
    },
    updateCharts() {
      var that = this
      var day_list = []
      for (var i = 0; i < that.confirm_list.length; i++) {
        day_list[i] = that.confirm_list[i][0].day
      }

      var option1 = {
        baseOption: {
          timeline: {
            axisType: 'category',
            autoPlay: true,
            playInterval: 4000,
            symbolSize: 12,
            left: '5%',
            right: '5%',
            bottom: '0%',
            width: '90%',
            data: day_list,
            tooltip: {
              formatter: day_list
            }
          },
          tooltip: {
            formatter: function(params, ticket, callback) {
              return params.seriesName + '<br />' + params.name + '：' + params.value
            }
          },
          toolbox: {
            feature: {
              dataView: { show: true, readOnly: false }
            }
          },
          visualMap: {
            min: 0,
            max: 2500,
            left: 'left',
            top: 'bottom',
            text: ['高', '低'],
            calculable: true,
            inRange: {
              color: ['#f5f5f5', '#ff5050', '#800000']
            },
            show: true
          },
          geo: {
            map: 'china',
            roam: true,
            zoom: 1.25,
            label: {
              show: true
            },
            itemStyle: {
              normal: {
                borderColor: 'rgba(0, 0, 0, 0.2)'
              }
            }
          }
        },
        options: []
      }
      var option2 = {
        baseOption: {
          timeline: {
            axisType: 'category',
            autoPlay: true,
            playInterval: 4000,
            symbolSize: 12,
            left: '5%',
            right: '5%',
            bottom: '0%',
            width: '90%',
            data: day_list,
            tooltip: {
              formatter: day_list
            }
          },
          toolbox: {
            feature: {
              dataView: { show: true, readOnly: false }
            }
          },
          tooltip: {
            formatter: function(params, ticket, callback) {
              return params.seriesName + '<br />' + params.name + '：' + params.value
            }
          },
          visualMap: {
            min: 0,
            max: 100,
            left: 'left',
            top: 'bottom',
            text: ['高', '低'],
            calculable: true,
            inRange: {
              color: ['#f5f5f5', '#ff5050']
            },
            show: true
          },
          geo: {
            map: 'china',
            roam: true,
            zoom: 1.25,
            label: {
              show: true
            },
            itemStyle: {
              normal: {
                borderColor: 'rgba(0, 0, 0, 0.2)'
              }
            }
          }
        },
        options: []
      }
      for (var j = 0; j < that.confirm_list.length; j++) {
        option1.options.push({
          title: {
            text: that.confirm_list[j][0].day + ' 累计确诊疫情趋势'
          },
          series: [{
            name: '累计确诊',
            type: 'map',
            geoIndex: 0,
            data: that.confirm_list[j]
          }]
        })
        option2.options.push({
          title: {
            text: that.current_list[j][0].day + ' 现存确诊疫情趋势'
          },
          series: [{
            name: '现存确诊',
            type: 'map',
            geoIndex: 0,
            data: that.current_list[j]
          }]
        })
      }

      that.china_map.setOption(option1)
      that.china_current_map.setOption(option2)
    }
  }
}

</script>

<style scoped>

</style>
