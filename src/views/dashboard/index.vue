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
    that.out_in = echarts.init(document.getElementById('out_in'), 'macarons')
    this.init_trend()
    this.fetch_china_data()
    this.update_word_cloud()
    this.update_out_in()
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
    },
    update_out_in() {
      var that = this
      var planePath = 'path://M1705.06,1318.313v-89.254l-319.9-221.799l0.073-208.063c0.521-84.662-26.629-121.796-63.961-121.491c-37.332-0.305-64.482,36.829-63.961,121.491l0.073,208.063l-319.9,221.799v89.254l330.343-157.288l12.238,241.308l-134.449,92.931l0.531,42.034l175.125-42.917l175.125,42.917l0.531-42.034l-134.449-92.931l12.238-241.308L1705.06,1318.313z'
      var geoCoordMap = {
        北京: [116.404184,39.914578],
        尼日利亚: [-4.388361, 11.186148],
        美国洛杉矶: [-118.24311, 34.052713],
        香港邦泰: [114.195466, 22.282751],
        美国芝加哥: [-87.801833, 41.870975],
        加纳库马西: [-4.62829, 7.72415],
        英国曼彻斯特: [-1.657222, 51.886863],
        德国汉堡: [10.01959, 54.38474],
        哈萨克斯坦阿拉木图: [45.326912, 41.101891],
        俄罗斯伊尔库茨克: [89.116876, 67.757906],
        巴西: [-48.678945, -10.493623],
        埃及达米埃塔: [31.815593, 31.418032],
        西班牙巴塞罗纳: [2.175129, 41.385064],
        柬埔寨金边: [104.88659, 11.545469],
        意大利米兰: [9.189948, 45.46623],
        乌拉圭蒙得维的亚: [-56.162231, -34.901113],
        莫桑比克马普托: [32.608571, -25.893473],
        阿尔及利亚阿尔及尔: [3.054275, 36.753027],
        阿联酋迪拜: [55.269441, 25.204514],
        匈牙利布达佩斯: [17.108519, 48.179162],
        澳大利亚悉尼: [150.993137, -33.675509],
        美国加州: [-121.910642, 41.38028],
        澳大利亚墨尔本: [144.999416, -37.781726],
        墨西哥: [-99.094092, 19.365711],
        加拿大温哥华: [-123.023921, 49.311753],
        '阿富汗': [69.11, 34.28],
        '阿尔巴尼亚': [19.49, 41.18],
        '阿尔及利亚': [3.08, 36.42],
        '美属萨摩亚': [-170.43, -14.16],
        '安道尔': [1.32, 42.31],
        '安哥拉': [13.15, -8.50],
        '安提瓜和巴布达': [-61.48, 17.20],
        '阿根廷': [-60.00, -36.30],
        '亚美尼亚': [44.31, 40.10],
        '阿鲁巴': [-70.02, 12.32],
        '澳大利亚': [149.08, -35.15],
        '奥地利': [16.22, 48.12],
        '阿塞拜疆': [49.56, 40.29],
        '巴哈马': [-77.20, 25.05],
        '巴林': [50.30, 26.10],
        '孟加拉国': [90.26, 23.43],
        '巴巴多斯': [-59.30, 13.05],
        '白俄罗斯': [27.30, 53.52],
        '比利时': [4.21, 50.51],
        '伯利兹': [-88.30, 17.18],
        '贝宁': [2.42, 6.23],
        '不丹': [89.45, 27.31],
        '玻利维亚': [-68.10, -16.20],
        '波斯尼亚和黑塞哥维那': [18.26, 43.52],
        '博茨瓦纳': [25.57, -24.45],
        '巴西': [-47.55, -15.47],
        '英属维尔京群岛': [-64.37, 18.27],
        '文莱': [115.00, 4.52],
        '保加利亚': [23.20, 42.45],
        '布基纳法索': [-1.30, 12.15],
        '布隆迪': [29.18, -3.16],
        '柬埔寨': [104.55, 11.33],
        '喀麦隆': [11.35, 3.50],
        '加拿大': [-75.42, 45.27],
        '佛得角': [-23.34, 15.02],
        '开曼群岛': [-81.24, 19.20],
        '中非共和国': [18.35, 4.23],
        '乍得': [14.59, 12.10],
        '智利': [-70.40, -33.24],
        '中国': [116.20, 39.55],
        '哥伦比亚': [-74.00, 4.34],
        '科摩罗': [43.16, -11.40],
        '刚果': [15.12, -4.09],
        '哥斯达黎加': [-84.02, 9.55],
        '科特迪瓦': [-5.17, 6.49],
        '克罗地亚': [15.58, 45.50],
        '古巴': [-82.22, 23.08],
        '塞浦路斯': [33.25, 35.10],
        '捷克共和国': [14.22, 50.05],
        '朝鲜': [125.30, 39.09],
        '刚果(扎伊尔)': [15.15, -4.20],
        '丹麦': [12.34, 55.41],
        '吉布提': [42.20, 11.08],
        '多米尼加': [-61.24, 15.20],
        '多米尼加共和国': [-69.59, 18.30],
        '东帝汶': [125.34, -8.29],
        '厄瓜多尔': [-78.35, -0.15],
        '埃及': [31.14, 30.01],
        '萨尔瓦多': [-89.10, 13.40],
        '赤道几内亚': [8.50, 3.45],
        '厄立特里亚': [38.55, 15.19],
        '爱沙尼亚': [24.48, 59.22],
        '埃塞俄比亚': [38.42, 9.02],
        '福克兰群岛(马尔维纳斯群岛)': [-59.51, -51.40],
        '法罗群岛': [-6.56, 62.05],
        '斐济': [178.30, -18.06],
        '芬兰': [25.03, 60.15],
        '法国': [2.20, 48.50],
        '法属圭亚那': [-52.18, 5.05],
        '法属波利尼西亚': [-149.34, -17.32],
        '加蓬': [9.26, 0.25],
        '冈比亚': [-16.40, 13.28],
        '格鲁吉亚': [44.50, 41.43],
        '德国': [13.25, 52.30],
        '加纳': [-0.06, 5.35],
        '希腊': [23.46, 37.58],
        '格陵兰': [-51.35, 64.10],
        '瓜德罗普岛': [-61.44, 16.00],
        '危地马拉': [-90.22, 14.40],
        '根西岛': [-2.33, 49.26],
        '几内亚': [-13.49, 9.29],
        '几内亚比绍': [-15.45, 11.45],
        '圭亚那': [-58.12, 6.50],
        '海地': [-72.20, 18.40],
        '赫德岛和麦当劳群岛': [74.00, -53.00],
        '洪都拉斯': [-87.14, 14.05],
        '匈牙利': [19.05, 47.29],
        '冰岛': [-21.57, 64.10],
        '印度': [77.13, 28.37],
        '印度尼西亚': [106.49, -6.09],
        '伊朗': [51.30, 35.44],
        '伊拉克': [44.30, 33.20],
        '爱尔兰': [-6.15, 53.21],
        '以色列': [35.12, 31.47],
        '意大利': [12.29, 41.54],
        '牙买加': [-76.50, 18.00],
        '约旦': [35.52, 31.57],
        '哈萨克斯坦': [71.30, 51.10],
        '肯尼亚': [36.48, -1.17],
        '基里巴斯': [173.00, 1.30],
        '科威特': [48.00, 29.30],
        '吉尔吉斯斯坦': [74.46, 42.54],
        '老挝': [102.36, 17.58],
        '拉脱维亚': [24.08, 56.53],
        '黎巴嫩': [35.31, 33.53],
        '莱索托': [27.30, -29.18],
        '利比里亚': [-10.47, 6.18],
        '阿拉伯利比亚民众国': [13.07, 32.49],
        '列支敦士登': [9.31, 47.08],
        '立陶宛': [25.19, 54.38],
        '卢森堡': [6.09, 49.37],
        '马达加斯加': [47.31, -18.55],
        '马拉维': [33.48, -14.00],
        '马来西亚': [101.41, 3.09],
        '马尔代夫': [73.28, 4.00],
        '马里': [-7.55, 12.34],
        '马耳他': [14.31, 35.54],
        '马提尼克岛': [-61.02, 14.36],
        '毛里塔尼亚': [57.30, -20.10],
        '马约特岛': [45.14, -12.48],
        '墨西哥': [-99.10, 19.20],
        '密克罗尼西亚(联邦) ': [158.09, 6.55],
        '摩尔多瓦共和国': [28.50, 47.02],
        '莫桑比克': [32.32, -25.58],
        '缅甸': [96.20, 16.45],
        '纳米比亚': [17.04, -22.35],
        '尼泊尔': [85.20, 27.45],
        '荷兰': [4.54, 52.23],
        '荷属安的列斯': [-69.00, 12.05],
        '新喀里多尼亚': [166.30, -22.17],
        '新西兰': [174.46, -41.19],
        '尼加拉瓜': [-86.20, 12.06],
        '尼日尔': [2.06, 13.27],
        '尼日利亚': [7.32, 9.05],
        '诺福克岛': [168.43, -45.20],
        '北马里亚纳群岛': [145.45, 15.12],
        '挪威': [10.45, 59.55],
        '阿曼': [58.36, 23.37],
        '巴基斯坦': [73.10, 33.40],
        '帕劳': [134.28, 7.20],
        '巴拿马': [-79.25, 9.00],
        '巴布亚新几内亚': [147.08, -9.24],
        '巴拉圭': [-57.30, -25.10],
        '秘鲁': [-77.00, -12.00],
        '菲律宾': [121.03, 14.40],
        '波兰': [21.00, 52.13],
        '葡萄牙': [-9.10, 38.42],
        '波多黎各': [-66.07, 18.28],
        '卡塔尔': [51.35, 25.15],
        '韩国': [126.58, 37.31],
        '罗马尼亚': [26.10, 44.27],
        '俄罗斯': [37.35, 55.45],
        '卢旺达': [30.04, -1.59],
        '圣基茨和尼维斯': [-62.43, 17.17],
        '圣卢西亚': [-60.58, 14.02],
        '圣皮埃尔和密克隆': [-56.12, 46.46],
        '圣文森特和格林纳丁斯': [-61.10, 13.10],
        '萨摩亚': [-171.50, -13.50],
        '圣马力诺': [12.30, 43.55],
        '圣多美和普林西比': [6.39, 0.10],
        '沙特阿拉伯': [46.42, 24.41],
        '塞内加尔': [-17.29, 14.34],
        '塞拉利昂': [-13.17, 8.30],
        '斯洛伐克': [17.07, 48.10],
        '斯洛文尼亚': [14.33, 46.04],
        '所罗门群岛': [159.57, -9.27],
        '索马里': [45.25, 2.02],
        '比勒陀利亚': [28.12, -25.44],
        '西班牙': [-3.45, 40.25],
        '苏丹': [32.35, 15.31],
        '苏里南': [-55.10, 5.50],
        '斯威士兰': [31.06, -26.18],
        '瑞典': [18.03, 59.20],
        '瑞士': [7.28, 46.57],
        '阿拉伯叙利亚共和国': [36.18, 33.30],
        '塔吉克斯坦': [68.48, 38.33],
        '泰国': [100.35, 13.45],
        '马其顿': [21.26, 42.01],
        '多哥': [1.20, 6.09],
        '汤加': [-174.00, -21.10],
        '突尼斯': [10.11, 36.50],
        '土耳其': [32.54, 39.57],
        '土库曼斯坦': [57.50, 38.00],
        '图瓦卢': [179.13, -8.31],
        '乌干达': [32.30, 0.20],
        '乌克兰': [30.28, 50.30],
        '阿联酋': [54.22, 24.28],
        '英国': [-0.05, 51.36],
        '坦桑尼亚': [35.45, -6.08],
        '美国': [-77.02, 39.91],
        '美属维尔京群岛': [-64.56, 18.21],
        '乌拉圭': [-56.11, -34.50],
        '乌兹别克斯坦': [69.10, 41.20],
        '瓦努阿图': [168.18, -17.45],
        '委内瑞拉': [-66.55, 10.30],
        '越南': [105.55, 21.05],
        '南斯拉夫': [20.37, 44.50],
        '赞比亚': [28.16, -15.28],
        '津巴布韦': [31.02, -17.43]
      }
      var BJData = [
        [{
          name: '尼日利亚',
          value: 9100
        }, {
          name: '北京'
        }],
        [{
          name: '美国洛杉矶',
          value: 2370
        }, {
          name: '北京'
        }],
        [{
          name: '香港邦泰',
          value: 3130
        }, {
          name: '北京'
        }],
        [{
          name: '美国芝加哥',
          value: 2350
        }, {
          name: '北京'
        }],
        [{
          name: '加纳库马西',
          value: 5120
        }, {
          name: '北京'
        }],
        [{
          name: '英国曼彻斯特',
          value: 3110
        }, {
          name: '北京'
        }],
        [{
          name: '德国汉堡',
          value: 6280
        }, {
          name: '北京'
        }],
        [{
          name: '哈萨克斯坦阿拉木图',
          value: 7255
        }, {
          name: '北京'
        }],
        [{
          name: '俄罗斯伊尔库茨克',
          value: 8125
        }, {
          name: '北京'
        }],
        [{
          name: '巴西',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '埃及达米埃塔',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '西班牙巴塞罗纳',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '柬埔寨金边',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '意大利米兰',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '乌拉圭蒙得维的亚',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '莫桑比克马普托',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '阿尔及利亚阿尔及尔',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '阿联酋迪拜',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '匈牙利布达佩斯',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '澳大利亚悉尼',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '美国加州',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '澳大利亚墨尔本',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '墨西哥',
          value: 3590
        }, {
          name: '北京'
        }],
        [{
          name: '加拿大温哥华',
          value: 3590
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
