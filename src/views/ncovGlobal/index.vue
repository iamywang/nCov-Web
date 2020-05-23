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
      <el-card style="height: 700px; margin: 8px">
        <div id="global_map" :style="{width: '100%', height: '650px'}" />
      </el-card>
    </el-col>
    <el-col :span="24">
      <el-card style="height: 550px; margin: 8px">
        <div id="world_data" :style="{width: '100%', height: '500px'}" />
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
      global_dashboard: [5131920, 2789391, 2011645, 330894],
      global_top_confirm: [
        { value: 1621333, name: '美国' },
        { value: 326448, name: '俄罗斯' },
        { value: 228006, name: '意大利' },
        { value: 280117, name: '西班牙' },
        { value: 252246, name: '英国' },
        { value: 1866440, name: '其他' }
      ]
    }
  },
  mounted() {
    var that = this
    that.world_trend = echarts.init(document.getElementById('world_trend'), 'macarons')
    that.top_confirm = echarts.init(document.getElementById('top_confirm'), 'macarons')
    that.top_dead = echarts.init(document.getElementById('top_dead'), 'macarons')
    that.global_map = echarts.init(document.getElementById('global_map'), 'macarons')
    that.world_data = echarts.init(document.getElementById('world_data'), 'macarons')
    that.updateWorldData()
    that.init_trend()
    that.fetchList()
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
        title: [{
          text: 'Top累计确诊',
          subtext: search_date
        },
        {
          text: '{name|累计确诊' + '}\n{val|' + that.global_dashboard[0] + '}',
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
            name: '累计确诊',
            type: 'pie',
            radius: ['40%', '60%'],
            center: ['50%', '53%'],
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
      var nameMapList = {
        'Somalia': '索马里',
        'Liechtenstein': '列支敦士登',
        'Morocco': '摩洛哥',
        'W. Sahara': '西撒哈拉',
        'Serbia': '塞尔维亚',
        'Afghanistan': '阿富汗',
        'Angola': '安哥拉',
        'Albania': '阿尔巴尼亚',
        'Andorra': '安道尔共和国',
        'United Arab Emirates': '阿拉伯联合酋长国',
        'Argentina': '阿根廷',
        'Armenia': '亚美尼亚',
        'Australia': '澳大利亚',
        'Austria': '奥地利',
        'Azerbaijan': '阿塞拜疆',
        'Burundi': '布隆迪',
        'Belgium': '比利时',
        'Benin': '贝宁',
        'Burkina Faso': '布基纳法索',
        'Bangladesh': '孟加拉国',
        'Bulgaria': '保加利亚',
        'Bahrain': '巴林',
        'Bahamas': '巴哈马',
        'Bosnia and Herz.': '波斯尼亚和黑塞哥维那',
        'Belarus': '白俄罗斯',
        'Belize': '伯利兹',
        'Bermuda': '百慕大',
        'Bolivia': '玻利维亚',
        'Brazil': '巴西',
        'Barbados': '巴巴多斯',
        'Brunei': '文莱',
        'Bhutan': '不丹',
        'Botswana': '博茨瓦纳',
        'Central African Rep.': '中非',
        'Canada': '加拿大',
        'Switzerland': '瑞士',
        'Chile': '智利',
        'China': '中国',
        "Côte d'Ivoire": '科特迪瓦',
        'Cameroon': '喀麦隆',
        'Dem. Rep. Congo': '刚果民主共和国',
        'Congo': '刚果',
        'Colombia': '哥伦比亚',
        'Cape Verde': '佛得角',
        'Costa Rica': '哥斯达黎加',
        'Cuba': '古巴',
        'N. Cyprus': '北塞浦路斯',
        'Cyprus': '塞浦路斯',
        'Czech Rep.': '捷克',
        'Germany': '德国',
        'Djibouti': '吉布提',
        'Denmark': '丹麦',
        'Dominican Rep.': '多米尼加',
        'Algeria': '阿尔及利亚',
        'Ecuador': '厄瓜多尔',
        'Egypt': '埃及',
        'Eritrea': '厄立特里亚',
        'Spain': '西班牙',
        'Estonia': '爱沙尼亚',
        'Ethiopia': '埃塞俄比亚',
        'Finland': '芬兰',
        'Fiji': '斐济',
        'France': '法国',
        'Gabon': '加蓬',
        'United Kingdom': '英国',
        'Georgia': '格鲁吉亚',
        'Ghana': '加纳',
        'Guinea': '几内亚',
        'Gambia': '冈比亚',
        'Guinea-Bissau': '几内亚比绍',
        'Eq. Guinea': '赤道几内亚',
        'Greece': '希腊',
        'Grenada': '格林纳达',
        'Greenland': '格陵兰',
        'Guatemala': '危地马拉',
        'Guam': '关岛',
        'Guyana': '圭亚那',
        'Honduras': '洪都拉斯',
        'Croatia': '克罗地亚',
        'Haiti': '海地',
        'Hungary': '匈牙利',
        'Indonesia': '印度尼西亚',
        'India': '印度',
        'Br. Indian Ocean Ter.': '英属印度洋领土',
        'Ireland': '爱尔兰',
        'Iran': '伊朗',
        'Iraq': '伊拉克',
        'Iceland': '冰岛',
        'Israel': '以色列',
        'Italy': '意大利',
        'Jamaica': '牙买加',
        'Jordan': '约旦',
        'Japan': '日本',
        'Siachen Glacier': '锡亚琴冰川',
        'Kazakhstan': '哈萨克斯坦',
        'Kenya': '肯尼亚',
        'Kyrgyzstan': '吉尔吉斯坦',
        'Cambodia': '柬埔寨',
        'Korea': '韩国',
        'Kuwait': '科威特',
        'Lao PDR': '老挝',
        'Lebanon': '黎巴嫩',
        'Liberia': '利比里亚',
        'Libya': '利比亚',
        'Sri Lanka': '斯里兰卡',
        'Lesotho': '莱索托',
        'Lithuania': '立陶宛',
        'Luxembourg': '卢森堡',
        'Latvia': '拉脱维亚',
        'Moldova': '摩尔多瓦',
        'Madagascar': '马达加斯加',
        'Mexico': '墨西哥',
        'Macedonia': '马其顿',
        'Mali': '马里',
        'Malta': '马耳他',
        'Myanmar': '缅甸',
        'Montenegro': '黑山',
        'Mongolia': '蒙古',
        'Mozambique': '莫桑比克',
        'Mauritania': '毛里塔尼亚',
        'Mauritius': '毛里求斯',
        'Malawi': '马拉维',
        'Malaysia': '马来西亚',
        'Namibia': '纳米比亚',
        'New Caledonia': '新喀里多尼亚',
        'Niger': '尼日尔',
        'Nigeria': '尼日利亚',
        'Nicaragua': '尼加拉瓜',
        'Netherlands': '荷兰',
        'Norway': '挪威',
        'Nepal': '尼泊尔',
        'New Zealand': '新西兰',
        'Oman': '阿曼',
        'Pakistan': '巴基斯坦',
        'Panama': '巴拿马',
        'Peru': '秘鲁',
        'Philippines': '菲律宾',
        'Papua New Guinea': '巴布亚新几内亚',
        'Poland': '波兰',
        'Puerto Rico': '波多黎各',
        'Dem. Rep. Korea': '朝鲜',
        'Portugal': '葡萄牙',
        'Paraguay': '巴拉圭',
        'Palestine': '巴勒斯坦',
        'Qatar': '卡塔尔',
        'Romania': '罗马尼亚',
        'Russia': '俄罗斯',
        'Rwanda': '卢旺达',
        'Saudi Arabia': '沙特阿拉伯',
        'Sudan': '苏丹',
        'S. Sudan': '南苏丹',
        'Senegal': '塞内加尔',
        'Singapore': '新加坡',
        'Solomon Is.': '所罗门群岛',
        'Sierra Leone': '塞拉利昂',
        'El Salvador': '萨尔瓦多',
        'Suriname': '苏里南',
        'Slovakia': '斯洛伐克',
        'Slovenia': '斯洛文尼亚',
        'Sweden': '瑞典',
        'Swaziland': '斯威士兰',
        'Seychelles': '塞舌尔',
        'Syria': '叙利亚',
        'Chad': '乍得',
        'Togo': '多哥',
        'Thailand': '泰国',
        'Tajikistan': '塔吉克斯坦',
        'Turkmenistan': '土库曼斯坦',
        'Timor-Leste': '东帝汶',
        'Tonga': '汤加',
        'Trinidad and Tobago': '特立尼达和多巴哥',
        'Tunisia': '突尼斯',
        'Turkey': '土耳其',
        'Tanzania': '坦桑尼亚',
        'Uganda': '乌干达',
        'Ukraine': '乌克兰',
        'Uruguay': '乌拉圭',
        'United States': '美国',
        'Uzbekistan': '乌兹别克斯坦',
        'Venezuela': '委内瑞拉',
        'Vietnam': '越南',
        'Vanuatu': '瓦努阿图',
        'Yemen': '也门',
        'South Africa': '南非',
        'Zambia': '赞比亚',
        'Zimbabwe': '津巴布韦',
        'Aland': '奥兰群岛',
        'American Samoa': '美属萨摩亚',
        'Fr. S. Antarctic Lands': '南极洲',
        'Antigua and Barb.': '安提瓜和巴布达',
        'Comoros': '科摩罗',
        'Curaçao': '库拉索岛',
        'Cayman Is.': '开曼群岛',
        'Dominica': '多米尼加',
        'Falkland Is.': '马尔维纳斯群岛（福克兰）',
        'Faeroe Is.': '法罗群岛',
        'Micronesia': '密克罗尼西亚',
        'Heard I. and McDonald Is.': '赫德岛和麦克唐纳群岛',
        'Isle of Man': '曼岛',
        'Jersey': '泽西岛',
        'Kiribati': '基里巴斯',
        'Saint Lucia': '圣卢西亚',
        'N. Mariana Is.': '北马里亚纳群岛',
        'Montserrat': '蒙特塞拉特',
        'Niue': '纽埃',
        'Palau': '帕劳',
        'Fr. Polynesia': '法属波利尼西亚',
        'S. Geo. and S. Sandw. Is.': '南乔治亚岛和南桑威奇群岛',
        'Saint Helena': '圣赫勒拿',
        'St. Pierre and Miquelon': '圣皮埃尔和密克隆群岛',
        'São Tomé and Principe': '圣多美和普林西比',
        'Turks and Caicos Is.': '特克斯和凯科斯群岛',
        'St. Vin. and Gren.': '圣文森特和格林纳丁斯',
        'U.S. Virgin Is.': '美属维尔京群岛',
        'Samoa': '萨摩亚'
      }
      var that = this
      var day_list = []
      for (var i = 0; i < that.confirm_list.length; i++) {
        day_list[i] = that.confirm_list[i][0].day
      }
      var global_option = {
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
            nameMap: nameMapList,
            roam: true,
            zoom: 1.25,
            label: {
              show: false
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
        global_option.options.push({
          title: {
            text: that.confirm_list[j][0].day + ' 全球累计确诊趋势'
          },
          series: [{
            name: '累计确诊',
            type: 'map',
            geoIndex: 0,
            data: that.confirm_list[j]
          }]
        })
      }
      that.global_map.setOption(global_option)
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
          if (app.currentIndex > option.dataZoom[0].end / 100 * dataLen - 2) {
            option.dataZoom[0].start = app.currentIndex / dataLen * 100
            option.dataZoom[0].end = app.currentIndex / dataLen * 100 + 7
          }

          that.world_data.setOption(option)
        }, 4000)
      }, 'jsonp2')
    }
  }
}

</script>

<style scoped>

</style>
