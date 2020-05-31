<template>
  <div class="app-container">
    <el-col :span="8">
      <el-card style="height: 330px; margin: 8px">
        <el-tag>要闻速递</el-tag>
        <el-timeline style="margin-left: -32px; margin-top: 8px">
          <el-timeline-item timestamp="2020.05.28 18:19" placement="top">
            <el-tag type="danger" effect="dark">最新</el-tag>
            <span>世卫组织：全球新冠肺炎确诊病例超过555万例</span>
          </el-timeline-item>
          <el-timeline-item timestamp="2020.05.28 16:46" placement="top">
            <span>菲律宾新增539例新冠确诊病例 创单日最大增幅</span>
          </el-timeline-item>
          <el-timeline-item timestamp="2020.05.28 15:33" placement="top">
            <span>俄罗斯新增8371例新冠肺炎确诊病例 累计近38万例</span>
          </el-timeline-item>
          <el-timeline-item timestamp="2020.05.28 11:35" placement="top">
            <span>印度新增6566例新冠肺炎确诊病例 累计逾15.8万例</span>
          </el-timeline-item>
        </el-timeline>
      </el-card>
      <el-card style="height: 330px; margin: 8px">
        <el-tag>今日热搜</el-tag>
        <div id="top_search" :style="{width: '100%', height: '280px'}" />
      </el-card>
    </el-col>
    <el-col :span="16">
      <el-card style="height: 330px; margin: 8px">
        <div id="ncov_info" :style="{width: '100%', height: '350px'}" />
      </el-card>
      <el-card style="height: 330px; margin: 8px">
        <el-tag>疫情词云</el-tag>
        <div id="word_cloud" :style="{width: '100%', height: '300px'}" />
      </el-card>
    </el-col>
  </div>
</template>

<script>
import echarts from 'echarts'
require('echarts-wordcloud')
require('echarts/theme/macarons')
export default {
  data() {
    return {
      top_search: null,
      ncov_info: null,
      word_cloud: null
    }
  },
  mounted() {
    var that = this
    that.top_search = echarts.init(document.getElementById('top_search'), 'macarons')
    that.word_cloud = echarts.init(document.getElementById('word_cloud'), 'macarons')
    that.ncov_info = echarts.init(document.getElementById('ncov_info'), 'macarons')
    that.init_search()
    that.init_know()
    that.init_cloud()
  },
  methods: {
    init_cloud() {
      var option = {
        tooltip: {
          show: true
        },
        series: [{
          type: 'wordCloud',
          gridSize: 6,
          shape: 'diamond',
          sizeRange: [12, 50],
          width: 800,
          height: 600,
          textStyle: {
            normal: {
              color: function() {
                return 'rgb(' + [
                  Math.round(Math.random() * 160),
                  Math.round(Math.random() * 160),
                  Math.round(Math.random() * 160)
                ].join(',') + ')'
              }
            },
            emphasis: {
              shadowBlur: 10,
              shadowColor: '#333'
            }
          },
          data: [{
            'name': '新冠肺炎愈后一般6个月内不会再得',
            'value': 23
          }, {
            'name': '女篮两连胜后大喊武汉加油',
            'value': 54
          }, {
            'name': '火神山医院开微博',
            'value': 22
          }, {
            'name': '医疗队女队员集体理平头和光头',
            'value': 25
          }, {
            'name': '缅怀疫情中逝去的人们',
            'value': 47
          }, {
            'name': '妨害疫情防控的违法行为',
            'value': 19
          }, {
            'name': '66岁重症患者8天快速康复',
            'value': 9
          }, {
            'name': '别让快递小哥一直等在寒风中',
            'value': 45
          }, {
            'name': '湖北以外地区新增病例数连降5天',
            'value': 7
          }, {
            'name': '女儿写给战疫一线爸爸的信',
            'value': 4
          }, {
            'name': '青海连续3天无新增病例',
            'value': 3
          }, {
            'name': '辽宁再派1000名医护人员驰援武汉',
            'value': 49
          }, {
            'name': '抗击新型肺炎第一线',
            'value': 86
          }, {
            'name': '12项疫情防控惠民政策',
            'value': 56
          }, {
            'name': '战疫一线的别样团圆',
            'value': 3
          }, {
            'name': '31省区市心理援助热线',
            'value': 6
          }, {
            'name': '元宵节亮灯为武汉加油',
            'value': 27
          }, {
            'name': '抗击新型肺炎我们在行动',
            'value': 107
          }, {
            'name': '疫情中的逆行者',
            'value': 92
          }, {
            'name': '一张图看懂新冠肺炎',
            'value': 20
          }]
        }]
      }
      this.word_cloud.setOption(option)
    },
    init_search() {
      var that = this
      var charts = {
        cityList: ['新型肺炎实时动态', '李文亮妻子发声明', '法国卢浮宫将于7月6日重开', '美国白宫已禁止人员出入', '湖北尚不具推动来鄂旅游条件'],
        cityData: [5, 4, 3, 2, 1]
      }
      var top10CityList = charts.cityList
      var top10CityData = charts.cityData
      var color = ['rgba(248,195,248', 'rgba(100,255,249', 'rgba(135,183,255', 'rgba(248,195,248', 'rgba(100,255,249']

      var lineY = []
      for (var i = 0; i < charts.cityList.length; i++) {
        var x = i
        if (x > color.length - 1) {
          x = color.length - 1
        }
        var data = {
          name: charts.cityList[i],
          color: color[x] + ')',
          value: top10CityData[i],
          itemStyle: {
            normal: {
              show: true,
              color: new echarts.graphic.LinearGradient(0, 0, 1, 0, [{
                offset: 0,
                color: color[x] + ', 0.3)'
              }, {
                offset: 1,
                color: color[x] + ', 1)'
              }], false),
              barBorderRadius: 10
            },
            emphasis: {
              shadowBlur: 15,
              shadowColor: 'rgba(0, 0, 0, 0.1)'
            }
          }
        }
        lineY.push(data)
      }

      var option = {
        title: {
          show: false
        },
        tooltip: {
          trigger: 'item'
        },
        grid: {
          borderWidth: 0,
          top: '10%',
          left: '5%',
          right: '15%',
          bottom: '3%'
        },
        color: color,
        yAxis: [{
          type: 'category',
          inverse: true,
          axisTick: {
            show: false
          },
          axisLine: {
            show: false
          },
          axisLabel: {
            show: false,
            inside: false
          },
          data: top10CityList
        }, {
          type: 'category',
          axisLine: {
            show: false
          },
          axisTick: {
            show: false
          },
          axisLabel: {
            show: true,
            inside: false,
            formatter: function(val) {
              return `${val}`
            }
          },
          splitArea: {
            show: false
          },
          splitLine: {
            show: false
          },
          data: top10CityData
        }],
        xAxis: {
          type: 'value',
          axisTick: {
            show: false
          },
          axisLine: {
            show: false
          },
          splitLine: {
            show: false
          },
          axisLabel: {
            show: false
          }
        },
        series: [{
          name: '',
          type: 'bar',
          zlevel: 2,
          barWidth: '10px',
          data: lineY,
          animationDuration: 1500,
          label: {
            normal: {
              show: true,
              position: [0, '-24px'],
              formatter: function(a, b) {
                return a.name
              }
            }
          }
        }],
        animationEasing: 'cubicOut'
      }
      that.top_search.setOption(option)
    },
    init_know() {
      var that = this
      var data2 = [{
        name: '疫情科普',
        label: {
          backgroundColor: '#682d9a'
        },
        children: [{
          name: '啥是新冠病毒',
          children: [{
            name: '传染源：暂未找到',
            label: {
              backgroundColor: '#021b58'
            },
            children: [{
              name: '绝非来自武汉',
              label: {
                backgroundColor: '#e2a700'
              }
            }]
          }, {
            name: '传播途径：呼吸道飞沫'
          }, {
            name: '易感人群：普遍易感'
          }]
        }, {
          name: '典型症状',
          children: [{
            name: '发热、乏力、干咳'
          }, {
            name: '潜伏期3—7天'
          }, {
            name: '重症病例一周出现呼吸困难'
          }]
        }, {
          name: '如何预防',
          children: [{
            name: '减少外出、佩戴口罩、保持卫生'
          }, {
            name: '不接触、购买和食用野生动物'
          }, {
            name: '个人和家人健康监测、注意营养'
          }]
        }, {
          name: '出现症状怎么办',
          children: [{
            name: '前往医院、及时报备、核酸检测'
          }, {
            name: '主动透露旅行史、接触史'
          }, {
            name: '密切接触者注意隔离'
          }]
        }]
      }]

      var option = {
        tooltip: {
          trigger: 'item',
          formatter: '{b}'
        },
        series: [{
          type: 'tree',
          name: '疫情科普',
          edgeShape: 'polyline',
          data: data2,
          top: '0%',
          left: '0%',
          symbolSize: 1,
          initialTreeDepth: 10,
          label: {
            normal: {
              position: 'center',
              verticalAlign: 'middle',
              align: 'left',
              backgroundColor: '#01380d',
              color: '#fff',
              padding: 3,
              formatter: [
                '{box|{b}}'
              ].join('\n'),
              rich: {
                box: {
                  height: 18,
                  color: '#fff',
                  padding: [0, 5],
                  align: 'center'
                }
              }
            }
          },
          leaves: {
            label: {
              normal: {
                position: 'center',
                verticalAlign: 'middle',
                align: 'left',
                backgroundColor: '#021b58',
                formatter: [
                  '{box|{b}}'
                ].join('\n'),
                rich: {
                  box: {
                    height: 18,
                    color: '#fff',
                    padding: [0, 5],
                    align: 'center'
                  }
                }
              }
            }
          },
          expandAndCollapse: true
        }]
      }
      that.ncov_info.setOption(option)
    }
  }
}
</script>

<style scoped>

</style>
