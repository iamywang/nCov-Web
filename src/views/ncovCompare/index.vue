<template>
  <div class="app-container">
    <el-row>
      <el-col :span="3" style="margin: 8px">
        <el-tag effect="dark">某天两地区疫情数据对比</el-tag>
      </el-col>
      <el-col :span="3" style="margin: 8px">
        <el-input v-model="place_to_search" placeholder="请输入省/城市名称" size="small" />
      </el-col>
      <el-col :span="3" style="margin: 8px">
        <el-input v-model="place_to_search_2" placeholder="请输入省/城市名称" size="small" />
      </el-col>
      <el-col :span="3" style="margin: 8px">
        <el-input v-model="date_to_search" placeholder="请输入日期" suffix-icon="el-icon-date" size="small" />
      </el-col>
      <el-col :span="6" style="margin: 8px">
        <el-button type="plain" size="small" icon="el-icon-search" @click="searchFunc_1">查询省疫情数据</el-button>
        <el-button type="plain" size="small" icon="el-icon-search" @click="searchFunc_1_ex">查询市疫情数据</el-button>
      </el-col>
    </el-row>
    <el-col :span="24">
      <el-card style="height: 100px; margin: 8px">
        <el-col :span="4">
          <el-row style="margin-bottom: 8px">
            <el-tag>地区</el-tag>
          </el-row>
          <el-row style="margin-bottom: 8px">
            <div style="font-size: 18px">{{ list[list.length - 1].place }}（{{ list[list.length - 1].day }}）</div>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>累计确诊</el-tag>
          </el-row>
          <el-row style="margin-bottom: 8px">
            <div style="font-size: 18px">{{ list[list.length - 1].confirm }}（新增{{ list[list.length - 1].new_confirm }}）</div>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>现存确诊</el-tag>
          </el-row>
          <el-row style="margin-bottom: 8px">
            <div style="font-size: 18px">{{ list[list.length - 1].current }}</div>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>累计治愈</el-tag>
            <span>{{ list[list.length - 1].cured }}（新增{{ list[list.length - 1].new_cured }}）</span>
          </el-row>
          <el-row style="margin-bottom: 8px">
            <el-tag>治愈率</el-tag>
            <span>{{ (list[list.length - 1].cured * 100 /list[list.length - 1].confirm).toFixed(2) }}%</span>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>累计死亡</el-tag>
            <span>{{ list[list.length - 1].dead }}（新增{{ list[list.length - 1].new_dead }}）</span>
          </el-row>
          <el-row>
            <el-tag style="margin-bottom: 8px">死亡率</el-tag>
            <span>{{ (list[list.length - 1].dead * 100 /list[list.length - 1].confirm).toFixed(2) }}%</span>
          </el-row>
        </el-col>
      </el-card>
    </el-col>
    <el-col :span="24">
      <el-card style="height: 100px; margin: 8px">
        <el-col :span="4">
          <el-row style="margin-bottom: 8px">
            <el-tag>地区</el-tag>
          </el-row>
          <el-row style="margin-bottom: 8px">
            <div style="font-size: 18px">{{ list_2[list_2.length - 1].place }}（{{ list_2[list_2.length - 1].day }}）</div>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>累计确诊</el-tag>
          </el-row>
          <el-row style="margin-bottom: 8px">
            <div style="font-size: 18px">{{ list_2[list_2.length - 1].confirm }}（新增{{ list_2[list_2.length - 1].new_confirm }}）</div>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>现存确诊</el-tag>
          </el-row>
          <el-row style="margin-bottom: 8px">
            <div style="font-size: 18px">{{ list_2[list_2.length - 1].current }}</div>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>累计治愈</el-tag>
            <span>{{ list_2[list_2.length - 1].cured }}（新增{{ list_2[list_2.length - 1].new_cured }}）</span>
          </el-row>
          <el-row style="margin-bottom: 8px">
            <el-tag>治愈率</el-tag>
            <span>{{ (list_2[list_2.length - 1].cured * 100 /list_2[list_2.length - 1].confirm).toFixed(2) }}%</span>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>累计死亡</el-tag>
            <span>{{ list_2[list_2.length - 1].dead }}（新增{{ list_2[list_2.length - 1].new_dead }}）</span>
          </el-row>
          <el-row>
            <el-tag style="margin-bottom: 8px">死亡率</el-tag>
            <span>{{ (list_2[list_2.length - 1].dead * 100 /list_2[list_2.length - 1].confirm).toFixed(2) }}%</span>
          </el-row>
        </el-col>
      </el-card>
    </el-col>
    <el-col :span="24">
      <el-col :span="12">
        <el-card style="height: 200px; margin: 8px">
          <div id="percent_chart" :style="{width: '100%', height: '200px'}" />
        </el-card>
      </el-col>
      <el-col :span="12">
        <el-card style="height: 200px; margin: 8px">
          <div id="percent_chart_2" :style="{width: '100%', height: '200px'}" />
        </el-card>
      </el-col>
    </el-col>
    <el-col :span="12">
      <el-card style="height: 400px; margin: 8px">
        <div id="confirm_chart" :style="{width: '100%', height: '350px'}" />
      </el-card>
    </el-col>
    <el-col :span="12">
      <el-card style="height: 400px; margin: 8px">
        <div id="current_chart" :style="{width: '100%', height: '350px'}" />
      </el-card>
    </el-col>
    <el-col :span="12">
      <el-card style="height: 400px; margin: 8px">
        <div id="cured_chart" :style="{width: '100%', height: '350px'}" />
      </el-card>
    </el-col>
    <el-col :span="12">
      <el-card style="height: 400px; margin: 8px">
        <div id="dead_chart" :style="{width: '100%', height: '350px'}" />
      </el-card>
    </el-col>
    <el-table :data="last_14.concat(last_14_2)" element-loading-text="正在查询" stripe border fit highlight-current-row>
      <el-table-column align="center" type="index" />
      <el-table-column align="center" label="日期" prop="day" />
      <el-table-column align="center" label="省/市" prop="place" />
      <el-table-column align="center" label="累计确诊" prop="confirm" />
      <el-table-column align="center" label="现存确诊" prop="current" />
      <el-table-column align="center" label="新增确诊" prop="new_confirm" />
      <el-table-column align="center" label="累计治愈" prop="cured" />
      <el-table-column align="center" label="新增治愈" prop="new_cured" />
      <el-table-column align="center" label="累计死亡" prop="dead" />
      <el-table-column align="center" label="新增死亡" prop="new_dead" />
    </el-table>
  </div>
</template>

<script>
import axios from 'axios'
import echarts from 'echarts'
require('echarts/theme/macarons')
require('echarts/theme/roma')

export default {
  data() {
    return {
      list: [
        { 'day': '2020-05-12', 'confirm': 84451, 'current': 237, 'cured': 79570, 'new_confirm': 1, 'new_cured': 37, 'new_dead': 1, 'dead': 4644, 'place': '全国' }
      ],
      list_2: [],
      last_14: [],
      last_14_2: [],
      place_to_search: '',
      place_to_search_2: '',
      date_to_search: '',
      confirm_chart: null,
      current_chart: null,
      cured_chart: null,
      dead_chart: null,
      percent_chart: null,
      percent_chart_2: null
    }
  },
  created() {
    var that = this
    that.list_2 = that.list
    that.last_14 = that.list
    that.last_14_2 = that.list
  },
  mounted() {
    var that = this
    that.confirm_chart = echarts.init(document.getElementById('confirm_chart'), 'macarons')
    that.current_chart = echarts.init(document.getElementById('current_chart'), 'macarons')
    that.cured_chart = echarts.init(document.getElementById('cured_chart'), 'roma')
    that.dead_chart = echarts.init(document.getElementById('dead_chart'), 'roma')
    that.percent_chart = echarts.init(document.getElementById('percent_chart'), 'roma')
    that.percent_chart_2 = echarts.init(document.getElementById('percent_chart_2'), 'roma')
    that.updateCharts()
  },
  methods: {
    searchFunc_1() {
      var that = this
      axios.get('/server/getProvince/', {
        params: {
          day: that.date_to_search,
          province: that.place_to_search
        }
      }).then(function(res) {
        that.list = res.data
        axios.get('/server/getProvinceLasts/', {
          params: {
            day: that.date_to_search,
            province: that.place_to_search
          }
        }).then(function(res2) {
          that.last_14 = res2.data
          axios.get('/server/getProvince/', {
            params: {
              day: that.date_to_search,
              province: that.place_to_search_2
            }
          }).then(function(res3) {
            that.list_2 = res3.data
            axios.get('/server/getProvinceLasts/', {
              params: {
                day: that.date_to_search,
                province: that.place_to_search_2
              }
            }).then(function(res4) {
              that.last_14_2 = res4.data
              that.updateCharts()
            })
          })
        })
      })
    },
    searchFunc_1_ex() {
      var that = this
      axios.get('/server/getCity/', {
        params: {
          day: that.date_to_search,
          city: that.place_to_search
        }
      }).then(function(res) {
        that.list = res.data
        axios.get('/server/getCityLasts/', {
          params: {
            day: that.date_to_search,
            city: that.place_to_search
          }
        }).then(function(res2) {
          that.last_14 = res2.data
          axios.get('/server/getCity/', {
            params: {
              day: that.date_to_search,
              city: that.place_to_search_2
            }
          }).then(function(res3) {
            that.list_2 = res3.data
            axios.get('/server/getCityLasts/', {
              params: {
                day: that.date_to_search,
                city: that.place_to_search_2
              }
            }).then(function(res4) {
              that.last_14_2 = res4.data
              that.updateCharts()
            })
          })
        })
      })
    },
    updateCharts() {
      var that = this
      var days_data = []
      var last_confirm_data = []
      var last_confirm_data_2 = []
      var last_current_data = []
      var last_current_data_2 = []
      var last_cured_data = []
      var last_cured_data_2 = []
      var last_dead_data = []
      var last_dead_data_2 = []
      for (var i = 0, len = that.last_14.length; i < len; i++) {
        days_data[i] = that.last_14[i].day
        last_confirm_data[i] = that.last_14[i].confirm
        last_confirm_data_2[i] = that.last_14_2[i].confirm
        last_current_data[i] = that.last_14[i].current
        last_current_data_2[i] = that.last_14_2[i].current
        last_cured_data[i] = that.last_14[i].cured
        last_cured_data_2[i] = that.last_14_2[i].cured
        last_dead_data[i] = that.last_14[i].dead
        last_dead_data_2[i] = that.last_14_2[i].dead
      }
      that.confirm_chart.setOption({
        title: {
          text: '确诊病例趋势'
        },
        xAxis: {
          data: days_data
        },
        yAxis: {},
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
          data: [that.list[0].place, that.list_2[0].place]
        },
        series: [{
          name: that.list[0].place,
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
          name: that.list_2[0].place,
          smooth: true,
          type: 'line',
          data: last_confirm_data_2,
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        }]
      })
      that.current_chart.setOption({
        title: {
          text: '现存确诊趋势'
        },
        xAxis: {
          data: days_data
        },
        yAxis: {},
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
          data: [that.list[0].place, that.list_2[0].place]
        },
        series: [{
          name: that.list[0].place,
          smooth: true,
          type: 'line',
          data: last_current_data,
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        },
        {
          name: that.list_2[0].place,
          smooth: true,
          type: 'line',
          data: last_current_data_2,
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        }]
      })
      that.cured_chart.setOption({
        title: {
          text: '累计治愈趋势'
        },
        xAxis: {
          data: days_data
        },
        yAxis: {},
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
          data: [that.list[0].place, that.list_2[0].place]
        },
        series: [{
          name: that.list[0].place,
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
          name: that.list_2[0].place,
          smooth: true,
          type: 'line',
          data: last_cured_data_2,
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        }]
      })
      that.dead_chart.setOption({
        title: {
          text: '累计死亡趋势'
        },
        xAxis: {
          data: days_data
        },
        yAxis: {},
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
          data: [that.list[0].place, that.list_2[0].place]
        },
        series: [{
          name: that.list[0].place,
          smooth: true,
          type: 'line',
          data: last_dead_data,
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        },
        {
          name: that.list_2[0].place,
          smooth: true,
          type: 'line',
          data: last_dead_data_2,
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        }]
      })
      that.percent_chart.setOption({
        title: {
          text: '两地区治愈率'
        },
        grid: {
          left: '2%',
          right: '2%',
          bottom: '2%',
          containLabel: true
        },
        tooltip: {
          trigger: 'item',
          formatter: '{a} <br/>{b}: {c}%'
        },
        series: [{
          name: '治愈率',
          type: 'pie',
          radius: ['35%', '55%'],
          avoidLabelOverlap: true,
          label: {
            position: 'outer'
          },
          data: [
            {
              value: (that.list[that.list.length - 1].cured * 100 / that.list[that.list.length - 1].confirm).toFixed(2),
              name: that.list[0].place
            },
            { value: (that.list_2[that.list_2.length - 1].cured * 100 / that.list_2[that.list_2.length - 1].confirm).toFixed(2),
              name: that.list_2[0].place
            }
          ]
        }]
      })
      that.percent_chart_2.setOption({
        title: {
          text: '两地区死亡率'
        },
        grid: {
          left: '2%',
          right: '2%',
          bottom: '2%',
          containLabel: true
        },
        tooltip: {
          trigger: 'item',
          formatter: '{a} <br/>{b}: {c}%'
        },
        series: [{
          name: '死亡率',
          type: 'pie',
          radius: ['35%', '55%'],
          avoidLabelOverlap: true,
          label: {
            position: 'outer'
          },
          data: [
            {
              value: (that.list[that.list.length - 1].dead * 100 / that.list[that.list.length - 1].confirm).toFixed(2),
              name: that.list[0].place
            },
            { value: (that.list_2[that.list_2.length - 1].dead * 100 / that.list_2[that.list_2.length - 1].confirm).toFixed(2),
              name: that.list_2[0].place
            }
          ]
        }]
      })
    }
  }
}
</script>

<style scoped>

</style>
