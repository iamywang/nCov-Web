<template>
  <div class="app-container">
    <el-col :span="24">
      <el-col :span="16">
        <el-col :span="12">
          <el-card style="height: 320px; margin: 8px">
            <div id="world_trend" :style="{width: '100%', height: '290px'}" />
          </el-card>
        </el-col>
        <el-col :span="12">
          <el-card style="height: 320px; margin: 8px">
            <div id="china_trend" :style="{width: '100%', height: '290px'}" />
          </el-card>
        </el-col>
        <el-card style="height: 420px; margin: 8px">
          <div id="word_cloud" :style="{width: '100%', height: '390px'}" />
        </el-card>
        <el-col :span="24">
          <el-card style="height: 400px; margin: 8px">
            <div id="china_line" :style="{width: '100%', height: '370px'}" />
          </el-card>
        </el-col>
      </el-col>
      <el-col :span="8">
        <el-card style="height: 300px; margin: 8px">
          <div id="out_in" :style="{width: '100%', height: '270px'}" />
        </el-card>
        <el-card style="height: 280px; margin: 8px">
          <div id="top_confirm" :style="{width: '100%', height: '250px'}" />
        </el-card>
        <el-card style="height: 280px; margin: 8px">
          <div id="top_dead" :style="{width: '100%', height: '250px'}" />
        </el-card>
        <el-card style="height: 280px; margin: 8px">
          <div id="top_province" :style="{width: '100%', height: '250px'}" />
        </el-card>
      </el-col>
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
      china_last_14: [],
      world_cloud: null,
      out_in: null,
      world_trend: null,
      china_trend: null,
      top_confirm: null,
      top_dead: null,
      top_province: null,
      china_line: null,
      main_province: null,
      china_scatter: [84522, 141, 79736, 4645],
      global_scatter: [5131920, 2789391, 2011645, 330894],
      global_confirm: [
        { value: 84522, name: '中国' },
        { value: 1621333, name: '美国' },
        { value: 326448, name: '俄罗斯' },
        { value: 228006, name: '意大利' },
        { value: 280117, name: '西班牙' },
        { value: 252246, name: '英国' },
        { value: 1866440, name: '其他' }
      ],
      china_top_pro: [
        { value: 68135, name: '湖北省' },
        { value: 1591, name: '广东省' },
        { value: 1276, name: '河南省' },
        { value: 1268, name: '浙江省' },
        { value: 12252, name: '其他' }
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
    that.out_in = echarts.init(document.getElementById('out_in'), 'macarons')
    this.init_trend()
    this.fetch_china_data()
    this.update_word_cloud()
    this.update_out_in()
  },
  methods: {
    init_trend() {
      var that = this
      var search_date = '2020-05-22'
      that.world_trend.setOption({
        title: {
          text: '世界疫情数据总览',
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
          name: '世界疫情数据总览',
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
            data: [28242, 27940, 32486, 36124, 96363],
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
        title: [{
          text: 'Top累计确诊（中国）',
          subtext: search_date
        },
        {
          text: '{name|累计确诊' + '}\n{val|' + that.china_scatter[0] + '}',
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
          formatter: '{a} <br/>{b}: {c} ({d}%)'
        },
        series: [{
          name: '数据对比',
          type: 'pie',
          radius: ['40%', '60%'],
          center: ['50%', '53%'],
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
          day: '2020-05-21'
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
    },
    update_out_in() {
      var that = this
      var planePath = 'path://M1705.06,1318.313v-89.254l-319.9-221.799l0.073-208.063c0.521-84.662-26.629-121.796-63.961-121.491c-37.332-0.305-64.482,36.829-63.961,121.491l0.073,208.063l-319.9,221.799v89.254l330.343-157.288l12.238,241.308l-134.449,92.931l0.531,42.034l175.125-42.917l175.125,42.917l0.531-42.034l-134.449-92.931l12.238-241.308L1705.06,1318.313z'
      var geoCoordMap = {
        '北京': [116.404184, 39.914578],
        '澳大利亚': [149.08, -35.15],
        '比利时': [4.21, 50.51],
        '加拿大': [-75.42, 45.27],
        '德国': [13.25, 52.30],
        '意大利': [12.29, 41.54],
        '荷兰': [4.54, 52.23],
        '菲律宾': [121.03, 14.40],
        '韩国': [126.58, 37.31],
        '俄罗斯': [37.35, 55.45],
        '英国': [-0.05, 51.36],
        '美国': [-77.02, 39.91]
      }
      var BJData = [
        [{
          name: '澳大利亚',
          value: 100
        }, {
          name: '北京'
        }],
        [{
          name: '比利时',
          value: 100
        }, {
          name: '北京'
        }],
        [{
          name: '德国',
          value: 100
        }, {
          name: '北京'
        }],
        [{
          name: '加拿大',
          value: 100
        }, {
          name: '北京'
        }],
        [{
          name: '意大利',
          value: 100
        }, {
          name: '北京'
        }],
        [{
          name: '美国',
          value: 100
        }, {
          name: '北京'
        }],
        [{
          name: '荷兰',
          value: 100
        }, {
          name: '北京'
        }],
        [{
          name: '菲律宾',
          value: 100
        }, {
          name: '北京'
        }],
        [{
          name: '韩国',
          value: 100
        }, {
          name: '北京'
        }],
        [{
          name: '俄罗斯',
          value: 100
        }, {
          name: '北京'
        }],
        [{
          name: '英国',
          value: 100
        }, {
          name: '北京'
        }]
      ]
      var convertData = function(data) {
        var res = []
        for (var i = 0; i < data.length; i++) {
          var dataItem = data[i]
          var fromCoord = geoCoordMap[dataItem[0].name]
          var toCoord = geoCoordMap[dataItem[1].name]
          if (fromCoord && toCoord) {
            res.push({
              fromName: dataItem[0].name,
              toName: dataItem[1].name,
              coords: [fromCoord, toCoord],
              value: dataItem[0].value
            })
          }
        }
        return res
      }

      var series = [];
      [
        ['北京', BJData]
      ].forEach(function(item, i) {
        series.push({
          name: item[2],
          type: 'lines',
          zlevel: 2,
          effect: {
            show: true,
            // //飞机的速度  这里是s单位
            period: 6,
            trailLength: 0,
            symbol: planePath,
            // 飞机大小
            symbolSize: 16
          },
          lineStyle: {
            normal: {
              color: '#ff8800',
              // 线条宽度
              width: 1,
              opacity: 1,
              curveness: 0.2
            }
          },
          label: {
            normal: {
              show: false,
              position: 'middle',
              formatter: '{b}'
            }
          },
          data: convertData(item[1])
        }, {
          type: 'effectScatter',
          coordinateSystem: 'geo',
          zlevel: 2,
          rippleEffect: {
            // 涟漪特效
            period: 8, // 动画时间，值越小速度越快
            brushType: 'stroke', // 波纹绘制方式 stroke, fill
            scale: 2 // 波纹圆环最大限制，值越大波纹越大
          },
          label: {
            normal: {
              show: false,
              position: 'right', // 显示位置
              offset: [5, 0], // 偏移设置
              formatter: '{b}', // 圆环显示文字
              textStyle: {
                color: 'red'
              }
            },
            emphasis: {
              show: true
            }
          },
          symbol: 'circle',
          color: '#62006d',
          symbolSize: function(val) {
            return 4 + val[2] / 1000 // 圆环大小
          },
          itemStyle: {
            normal: {
              show: false
            }
          },
          data: item[1].map(function(dataItem) {
            return {
              name: dataItem[0].name,
              value: geoCoordMap[dataItem[0].name].concat([dataItem[0].value])
            }
          })
        },
        // 被攻击点
        {
          type: 'scatter',
          coordinateSystem: 'geo',
          zlevel: 2,
          rippleEffect: {
            period: 4,
            brushType: 'stroke',
            scale: 4
          },
          label: {
            normal: {
              show: true,
              position: 'right',
              color: '#23580f',
              formatter: '{b}',
              textStyle: {
                color: '#360bf3'
              }
            },
            emphasis: {
              show: true
            }
          },
          symbol: 'pin',
          symbolSize: 30,
          itemStyle: {
            normal: {
              show: true,
              color: '#00215a'
            }
          },
          data: [{
            name: item[0],
            value: geoCoordMap[item[0]].concat([10])
          }]
        }
        )
      })

      var outin_option = {
        title: {
          text: '疫情输入概览'
        },
        tooltip: {
          trigger: 'item',
          formatter: ''
        },
        geo: {
          map: 'world',
          label: {
            emphasis: {
              show: false
            }
          },
          roam: true, // 是否允许缩放
          layoutCenter: ['50%', '50%'], // 地图位置
          layoutSize: '125%',
          itemStyle: {
            normal: {
              color: 'rgba(48,97,186,0.3)', // 地图背景色
              borderColor: 'rgba(0, 0, 0, 0)' // 省市边界线
            },
            emphasis: {
              color: 'rgba(48,97,186,0.3)' // 悬浮背景
            }
          }
        },
        series: series
      }
      that.out_in.setOption(outin_option)
    }
  }
}

</script>

<style scoped>

</style>
