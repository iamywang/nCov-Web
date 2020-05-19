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
        <div id="world_data" :style="{width: '100%', height: '700px'}" />
      </el-card>
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
      world_data: null,
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
    that.world_data = echarts.init(document.getElementById('global_map'), 'macarons')
    that.updateWorldData()
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
        that.updateCharts()
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
    },
    updateWorldData() {
      var that = this

      function jsonp(url, callback, callbackname) {
        window[callbackname] = callback
        // 创建一个script节点
        var script = document.createElement('script')
        script.src = url
        script.type = 'text/javascript'
        document.getElementsByTagName('body')[0].appendChild(script)
        setTimeout(function() {
          document.body.removeChild(script)
        }, 500)
      }

      var jsonpURL = 'https://m.look.360.cn/events/feiyan?sv=&version=&market=&device=2&net=4&stype=&scene=&sub_scene=&refer_scene=&refer_subscene=&f=jsonp&location=true&_=1583145636129&callback=jsonp2'
      // 调用jsonp函数发送jsonp请求的callback
      jsonp(jsonpURL, function(data) {
        console.log(data.country)
        var chartsJSON = data.country // 发送请求成功后开始执行，data是请求成功后，返回的数据
        var provinceName = [] // 国家
        var diagnosed = [] // 确诊
        var diffDiagnosed = []; var // 新增确诊
          cured = [] // 治愈
        var died = [] // 死亡

        // 取出每一条数据value,作为显示数据
        chartsJSON.forEach(function(items, index) {
          provinceName.push(items.provinceName)
          diagnosed.push(items.diagnosed)
          diffDiagnosed.push(items.diffDiagnosed)
          cured.push(items.cured)
          died.push(items.died)
        })
        // eslint-disable-next-line no-eval
        var sumDiagnosed = eval(diagnosed.join('+'))
        // eslint-disable-next-line no-eval
        var sumDiffDiagnosed = eval(diffDiagnosed.join('+'))
        // eslint-disable-next-line no-eval
        var sumCured = eval(cured.join('+'))
        // eslint-disable-next-line no-eval
        var sumDied = eval(died.join('+'))

        const mydate = new Date()
        var jsonMonth = mydate.getMonth() + 1
        var subDay = mydate.getDate()
        // eslint-disable-next-line no-self-assign
        subDay < 10 ? (subDay = '0' + subDay) : (subDay = subDay)
        var subDate = mydate.getFullYear() + '年' + jsonMonth + '月' + subDay + '日'
        // console.log(subDate)
        var subFunc = [
          '截止: ' + subDate + '\n' +
          '累计确诊: {a| ' + sumDiagnosed + '}', '新增确诊: {b| ' + sumDiffDiagnosed + '}', '累计治愈: {c| ' + sumCured + '}', '累计死亡: {d| ' + sumDied + '}'
        ].join('\xa0\xa0')

        // 基于准备好的dom，初始化echarts实例
        var option = {
          title: {
            text: '全球疫情趋势图',
            itemGap: 5,
            subtext: subFunc,
            subtextStyle: {
              color: '#333',
              fontSize: 14,
              rich: {
                a: {
                  color: '#b61e33',
                  fontSize: 15
                },
                b: {
                  color: '#ff6600',
                  fontSize: 15
                },
                c: {
                  color: 'rgb(17, 191, 199)',
                  fontSize: 15
                },
                d: {
                  color: 'gray',
                  fontSize: 15
                }
              }
            },
            x: 'center'
          },
          tooltip: {
            trigger: 'axis', // axis , item
            axisPointer: {
              type: 'cross'
            },
            x: 'left',
            textStyle: {
              fontSize: 14
            }
          },
          grid: {
            top: '12%',
            right: '2%',
            bottom: '10%',
            left: '7%'
          },
          legend: {
            data: ['确诊', '新增确诊', '治愈', '死亡'],
            x: 'right'
          },
          xAxis: {
            type: 'category',
            axisLabel: {
              rotate: 45,
              interval: 0,
              textStyle: {
                color: '#333',
                fontSize: 12
              },
              formatter: '{value}'
            },
            axisLine: {
              lineStyle: {
                color: '#ccc',
                width: 1
              }
            },
            splitLine: {
              show: true,
              lineStyle: {
                color: 'rgba(102,102,102,.1)', // 纵向网格线颜色
                width: 1,
                type: 'dashed'
              }
            },
            axisTick: {
              show: true // 坐标轴小标记
            },
            data: provinceName // 载入横坐标数据
          },
          yAxis: {
            type: 'value',
            axisLabel: {
              show: true,
              textStyle: {
                color: '#333',
                fontSize: 12
              },
              formatter: '{value}'
            },
            splitNumber: 4, // y轴刻度设置(值越大刻度越小)
            axisLine: {
              lineStyle: {
                color: '#ccc',
                width: 1
              }
            },
            splitLine: {
              show: true,
              lineStyle: {
                color: 'rgba(102,102,102,.1)', // 横向网格线颜色
                width: 1,
                type: 'dashed'
              }
            }
          },
          color: ['#b61e33', 'rgb(255, 160, 25)', 'rgb(17, 191, 199)', 'rgba(77, 80, 84, 0.7)'],
          dataZoom: [
            {
              type: 'inside',
              start: 0,
              end: 7
            }
          ],
          series: [{
            name: '确诊',
            type: 'bar', // line
            symbol: 'pin', // 曲线点样式 'emptyCircle', circle', 'rect', 'roundRect', 'triangle', 'diamond', 'pin', 'arrow', 'none'
            symbolSize: 12, // 曲线点大小
            label: {
              normal: {
                show: true,
                position: 'top'
              }
            },
            lineStyle: {
              normal: {
                width: 2
              }
            },
            smooth: true,
            data: diagnosed // 载入数据
          },
          {
            name: '新增确诊',
            type: 'bar', // line
            symbol: 'pin', // 曲线点样式 'emptyCircle', circle', 'rect', 'roundRect', 'triangle', 'diamond', 'pin', 'arrow', 'none'
            symbolSize: 12, // 曲线点大小
            label: {
              normal: {
                show: true,
                position: 'top'
              }
            },
            lineStyle: {
              normal: {
                width: 2
              }
            },
            smooth: true,
            data: diffDiagnosed // 载入数据
          },
          {
            name: '治愈',
            type: 'bar', // line
            symbol: 'pin', // 曲线点样式 'emptyCircle', circle', 'rect', 'roundRect', 'triangle', 'diamond', 'pin', 'arrow', 'none'
            symbolSize: 12, // 曲线点大小
            label: {
              normal: {
                show: true,
                position: 'top'
              }
            },
            lineStyle: {
              normal: {
                width: 2
              }
            },
            smooth: true,
            data: cured // 载入数据
          },
          {
            name: '死亡',
            type: 'bar', // line
            symbol: 'pin', // 曲线点样式 'emptyCircle', circle', 'rect', 'roundRect', 'triangle', 'diamond', 'pin', 'arrow', 'none'
            symbolSize: 12, // 曲线点大小
            label: {
              normal: {
                show: true,
                position: 'top'
              }
            },
            lineStyle: {
              normal: {
                width: 2
              }
            },
            smooth: true,
            data: died // 载入数据
          }
          ]
        }

        // 使用刚指定的配置项和数据显示图表。
        that.world_data.setOption(option)

        var app = {
          currentIndex: -1
        }
        setInterval(function() {
          var dataLen = option.series[0].data.length

          // 取消之前高亮的图形
          that.world_data.dispatchAction({
            type: 'downplay',
            seriesIndex: 0,
            dataIndex: app.currentIndex
          })
          // 感谢网友j***o的优化方案
          // option.dataZoom[0].start= app.currentIndex + 1;
          // option.dataZoom[0].end=app.currentIndex + 8;
          // console.log(app.currentIndex, option.dataZoom[0].end / 100 * dataLen - 2)
          if (app.currentIndex > option.dataZoom[0].end / 100 * dataLen - 2) {
            option.dataZoom[0].start = app.currentIndex / dataLen * 100
            option.dataZoom[0].end = app.currentIndex / dataLen * 100 + 7
          }

          that.world_data.setOption(option)

          app.currentIndex = (app.currentIndex + 1) % dataLen

          // 高亮当前图形
          that.world_data.dispatchAction({
            type: 'highlight',
            seriesIndex: 0,
            dataIndex: app.currentIndex
          })

          // 显示 tooltip
          that.world_data.dispatchAction({
            type: 'showTip',
            seriesIndex: 0,
            dataIndex: app.currentIndex
          })
        }, 3000)
      }, 'jsonp2')
    }
  }
}

</script>

<style scoped>

</style>
