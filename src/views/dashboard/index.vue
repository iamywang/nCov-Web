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
    <el-card style="height: 400px; margin: 8px">
      <div id="word_cloud" :style="{width: '100%', height: '360px'}" />
    </el-card>
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
      world_cloud: null,
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
    that.word_cloud = echarts.init(document.getElementById('word_cloud'), 'macarons')
    that.world_trend = echarts.init(document.getElementById('world_trend'), 'macarons')
    that.china_trend = echarts.init(document.getElementById('china_trend'), 'macarons')
    that.top_confirm = echarts.init(document.getElementById('top_confirm'), 'macarons')
    that.top_dead = echarts.init(document.getElementById('top_dead'), 'macarons')
    that.china_line = echarts.init(document.getElementById('china_line'), 'macarons')
    that.top_province = echarts.init(document.getElementById('top_province'), 'macarons')
    this.init_trend()
    this.fetch_china_data()
    this.update_word_cloud()
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
    },
    update_word_cloud() {
      var colorList = [[
        '#ff7f50', '#87cefa', '#da70d6', '#32cd32', '#6495ed',
        '#ff69b4', '#ba55d3', '#cd5c5c', '#ffa500', '#40e0d0',
        '#1e90ff', '#ff6347', '#7b68ee', '#d0648a', '#ffd700',
        '#6b8e23', '#4ea397', '#3cb371', '#b8860b', '#7bd9a5'
      ],
      [
        '#ff7f50', '#87cefa', '#da70d6', '#32cd32', '#6495ed',
        '#ff69b4', '#ba55d3', '#cd5c5c', '#ffa500', '#40e0d0',
        '#1e90ff', '#ff6347', '#7b68ee', '#00fa9a', '#ffd700',
        '#6b8e23', '#ff00ff', '#3cb371', '#b8860b', '#30e0e0'
      ],
      [
        '#929fff', '#9de0ff', '#ffa897', '#af87fe', '#7dc3fe',
        '#bb60b2', '#433e7c', '#f47a75', '#009db2', '#024b51',
        '#0780cf', '#765005', '#e75840', '#26ccd8', '#3685fe',
        '#9977ef', '#f5616f', '#f7b13f', '#f9e264', '#50c48f'
      ]][2]

      this.word_cloud.setOption({
        title: {
          text: '疫情关键词',
          x: 'center',
          y: 'top',
          textStyle: {
            fontSize: 18,
            fontWeight: 'bolder',
            color: '#333'
          }
        },
        tooltip: {},
        animationDurationUpdate: function(idx) {
          // 越往后的数据延迟越大
          return idx * 100
        },
        animationEasingUpdate: 'bounceIn',
        color: ['#fff', '#fff', '#fff'],
        series: [{
          type: 'graph',
          layout: 'force',
          force: {
            repulsion: 500,
            edgeLength: 10
          },
          roam: true,
          label: {
            normal: {
              show: true
            }
          },
          data: [{
            'name': '新冠肺炎愈后一般6个月内不会再得',
            'value': 2373,
            'symbolSize': 48,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[0],
                'color': colorList[0]
              }
            }
          }, {
            'name': '女篮两连胜后大喊武汉加油',
            'value': 5449,
            'symbolSize': 73,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[1],
                'color': colorList[1]
              }
            }
          }, {
            'name': '火神山医院开微博',
            'value': 2289,
            'symbolSize': 67,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[2],
                'color': colorList[2]
              }
            }
          }, {
            'name': '医疗队女队员集体理平头和光头',
            'value': 2518,
            'symbolSize': 50,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[3],
                'color': colorList[3]
              }
            }
          }, {
            'name': '缅怀疫情中逝去的人们',
            'value': 4730,
            'symbolSize': 88,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[4],
                'color': colorList[4]
              }
            }
          }, {
            'name': '妨害疫情防控的违法行为',
            'value': 1952,
            'symbolSize': 55,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[5],
                'color': colorList[5]
              }
            }
          }, {
            'name': '66岁重症患者8天快速康复',
            'value': 926,
            'symbolSize': 70,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[6],
                'color': colorList[6]
              }
            }
          }, {
            'name': '别让快递小哥一直等在寒风中',
            'value': 4536,
            'symbolSize': 67,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[7],
                'color': colorList[7]
              }
            }
          }, {
            'name': '湖北以外地区新增病例数连降5天',
            'value': 750,
            'symbolSize': 47,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[8],
                'color': colorList[8]
              }
            }
          }, {
            'name': '女儿写给战疫一线爸爸的信',
            'value': 493,
            'symbolSize': 82,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[9],
                'color': colorList[9]
              }
            }
          }, {
            'name': '青海连续3天无新增病例',
            'value': 385,
            'symbolSize': 59,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[10],
                'color': colorList[10]
              }
            }
          }, {
            'name': '辽宁再派1000名医护人员驰援武汉',
            'value': 4960,
            'symbolSize': 90,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[11],
                'color': colorList[11]
              }
            }
          }, {
            'name': '抗击新型肺炎第一线',
            'value': 8694000,
            'symbolSize': 134,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[12],
                'color': colorList[12]
              }
            }
          }, {
            'name': '12项疫情防控惠民政策',
            'value': 5668,
            'symbolSize': 75,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[13],
                'color': colorList[13]
              }
            }
          }, {
            'name': '战疫一线的别样团圆',
            'value': 339,
            'symbolSize': 68,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[14],
                'color': colorList[14]
              }
            }
          }, {
            'name': '31省区市心理援助热线',
            'value': 671,
            'symbolSize': 62,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[15],
                'color': colorList[15]
              }
            }
          }, {
            'name': '元宵节亮灯为武汉加油',
            'value': 27000,
            'symbolSize': 114,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[16],
                'color': colorList[16]
              }
            }
          }, {
            'name': '抗击新型肺炎我们在行动',
            'value': 10777000,
            'symbolSize': 130,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[17],
                'color': colorList[17]
              }
            }
          }, {
            'name': '疫情中的逆行者',
            'value': 92000,
            'symbolSize': 123,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[18],
                'color': colorList[18]
              }
            }
          }, {
            'name': '一张图看懂新冠肺炎',
            'value': 20000,
            'symbolSize': 141,
            'draggable': true,
            'itemStyle': {
              'normal': {
                'shadowBlur': 100,
                'shadowColor': colorList[19],
                'color': colorList[19]
              }
            },
            'url': 'https://gallery.echartsjs.com/preview.html?c=xPLngHx_Z&v=1'
          }]
        }]
      })
    }
  }
}

</script>

<style scoped>

</style>
