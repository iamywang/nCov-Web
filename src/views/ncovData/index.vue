<template>
  <div class="app-container">
    <el-row>
      <el-col :span="3" style="margin: 8px">
        <el-tag effect="dark">查询某天某省或市疫情数据</el-tag>
      </el-col>
      <el-col :span="3" style="margin: 8px">
        <el-input v-model="place_to_search" placeholder="请输入省/城市名称" size="small" />
      </el-col>
      <el-col :span="3" style="margin: 8px">
        <el-input v-model="date_to_search" placeholder="请输入日期" suffix-icon="el-icon-date" size="small" />
      </el-col>
      <el-col :span="6" style="margin: 8px">
        <el-button type="plain" size="small" icon="el-icon-search" @click="searchFunc_1">查询省疫情数据</el-button>
        <el-button type="plain" size="small" icon="el-icon-search" @click="searchFunc_1_ex">查询市疫情数据</el-button>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="3" style="margin: 8px">
        <el-tag effect="dark">查询某个日期时段疫情数据</el-tag>
      </el-col>
      <el-col :span="3" style="margin: 8px">
        <el-input v-model="place_to_search_mul" placeholder="请输入省/城市名称" size="small" />
      </el-col>
      <el-col :span="3" style="margin: 8px">
        <el-input v-model="date_to_search_st" placeholder="请输入开始日期" suffix-icon="el-icon-date" size="small" />
      </el-col>
      <el-col :span="3" style="margin: 8px">
        <el-input v-model="date_to_search_ed" placeholder="请输入结束日期" suffix-icon="el-icon-date" size="small" />
      </el-col>
      <el-col :span="6" style="margin: 8px">
        <el-button type="plain" size="small" icon="el-icon-search" @click="searchFunc_2">查询省疫情数据</el-button>
        <el-button type="plain" size="small" icon="el-icon-search" @click="searchFunc_2_ex">查询市疫情数据</el-button>
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
      <el-card style="height: 250px; margin: 8px">
        <el-col :span="5">
          <div id="pro_confirm_pie" :style="{width: '100%', height: '230px'}" />
        </el-col>
        <el-col :span="5">
          <div id="pro_current_pie" :style="{width: '100%', height: '230px'}" />
        </el-col>
        <el-col :span="7">
          <div id="pro_cured_chart" :style="{width: '100%', height: '220px'}" />
        </el-col>
        <el-col :span="7">
          <div id="pro_percent_chart" :style="{width: '100%', height: '220px'}" />
        </el-col>
      </el-card>
    </el-col>
    <el-col :span="12">
      <el-card style="height: 350px; margin: 8px">
        <div id="confirm_chart" :style="{width: '100%', height: '320px'}" />
      </el-card>
    </el-col>
    <el-col :span="12">
      <el-card style="height: 350px; margin: 8px">
        <div id="cured_chart" :style="{width: '100%', height: '320px'}" />
      </el-card>
    </el-col>
    <el-table :data="last_14" element-loading-text="正在查询" stripe border fit highlight-current-row>
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
      list: [{ 'day': '2020-05-11', 'confirm': 44, 'current': 0, 'cured': 44, 'new_confirm': 0, 'new_cured': 0, 'new_dead': 0, 'dead': 0, 'place': '潍坊' }],
      province_list: [{ 'day': '2020-05-11', 'confirm': 788, 'current': 4, 'cured': 777, 'new_confirm': 0, 'new_cured': 1, 'new_dead': 0, 'dead': 7, 'place': '山东省' }],
      last_14: [{ 'day': '2020-05-11', 'confirm': 44, 'current': 0, 'cured': 44, 'new_confirm': 0, 'new_cured': 0, 'new_dead': 0, 'dead': 0, 'place': '潍坊' }],
      place_to_search: '',
      place_to_search_mul: '',
      date_to_search: '',
      date_to_search_st: '',
      date_to_search_ed: '',
      confirm_chart: null,
      cured_chart: null,
      pro_confirm_pie: null,
      pro_current_pie: null,
      pro_cured_chart: null,
      pro_percent_chart: null
    }
  },
  mounted() {
    var that = this
    that.confirm_chart = echarts.init(document.getElementById('confirm_chart'), 'macarons')
    that.cured_chart = echarts.init(document.getElementById('cured_chart'), 'macarons')
    that.pro_confirm_pie = echarts.init(document.getElementById('pro_confirm_pie'), 'roma')
    that.pro_current_pie = echarts.init(document.getElementById('pro_current_pie'), 'roma')
    that.pro_cured_chart = echarts.init(document.getElementById('pro_cured_chart'), 'roma')
    that.pro_percent_chart = echarts.init(document.getElementById('pro_percent_chart'), 'roma')
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
        that.province_list = res.data
        axios.get('/server/getProvinceLasts/', {
          params: {
            day: that.date_to_search,
            province: that.place_to_search
          }
        }).then(function(res2) {
          that.last_14 = res2.data
          that.updateCharts()
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
        axios.get('/server/getProvince/', {
          params: {
            day: that.date_to_search,
            province: that.list[0].province
          }
        }).then(function(res2) {
          that.province_list = res2.data
          axios.get('/server/getCityLasts/', {
            params: {
              day: that.date_to_search,
              city: that.place_to_search
            }
          }).then(function(res3) {
            that.last_14 = res3.data
            that.updateCharts()
          })
        })
      })
    },
    searchFunc_2() {
      var that = this
      axios.get('/server/getProvinceSeries/', {
        params: {
          start: that.date_to_search_st,
          end: that.date_to_search_ed,
          province: that.place_to_search_mul
        }
      }).then(function(res) {
        that.list = res.data
        that.last_14 = res.data
        that.province_list = [that.list[that.list.length - 1]]
        that.updateCharts()
      })
    },
    searchFunc_2_ex() {
      var that = this
      axios.get('/server/getCitySeries/', {
        params: {
          start: that.date_to_search_st,
          end: that.date_to_search_ed,
          city: that.place_to_search_mul
        }
      }).then(function(res) {
        that.list = res.data
        that.last_14 = res.data
        axios.get('/server/getProvince/', {
          params: {
            day: that.date_to_search_ed,
            province: that.list[0].province
          }
        }).then(function(res2) {
          that.province_list = res2.data
          that.updateCharts()
        })
      })
    },
    updateCharts() {
      var that = this
      var days_data = []
      var last_confirm_data = []
      var last_current_data = []
      var last_cured_data = []
      var last_dead_data = []
      for (var i = 0, len = that.last_14.length; i < len; i++) {
        days_data[i] = that.last_14[i].day
        last_confirm_data[i] = that.last_14[i].confirm
        last_current_data[i] = that.last_14[i].current
        last_cured_data[i] = that.last_14[i].cured
        last_dead_data[i] = that.last_14[i].dead
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
        toolbox: {
          feature: {
            dataView: { show: true, readOnly: false }
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross'
          }
        },
        legend: {
          data: ['累计确诊', '现存确诊']
        },
        series: [{
          name: '累计确诊',
          smooth: true,
          type: 'line',
          data: last_confirm_data,
          color: '#f69e62',
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        },
        {
          name: '现存确诊',
          smooth: true,
          type: 'line',
          data: last_current_data,
          color: '#d349dc',
          markPoint: {
            data: [
              { type: 'min', name: '最小值' }
            ]
          }
        }]
      })
      that.cured_chart.setOption({
        title: {
          text: '治愈和死亡病例趋势'
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
        toolbox: {
          feature: {
            dataView: { show: true, readOnly: false }
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross'
          }
        },
        legend: {
          data: ['累计治愈', '累计死亡']
        },
        series: [{
          name: '累计治愈',
          smooth: true,
          type: 'line',
          data: last_cured_data,
          color: '#12710a',
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        },
        {
          name: '累计死亡',
          smooth: true,
          type: 'line',
          data: last_dead_data,
          color: '#804040',
          markPoint: {
            data: [
              { type: 'max', name: '最大值' }
            ]
          }
        }]
      })
      that.pro_confirm_pie.setOption({
        title: [{
          text: '全省累计确诊占比'
        },
        {
          text: '{name|占比' + '}\n{val|' + (that.list[that.list.length - 1].confirm * 100 / that.province_list[0].confirm).toFixed(2) + '%}',
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
                fontSize: 18,
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
          radius: ['35%', '55%'],
          center: ['50%', '53%'],
          avoidLabelOverlap: true,
          label: {
            position: 'outer'
          },
          data: [
            { value: that.list[that.list.length - 1].confirm, name: '累计确诊' },
            { value: that.province_list[0].confirm - that.list[that.list.length - 1].confirm, name: '其余地区' }
          ]
        }]
      })
      that.pro_current_pie.setOption({
        title: [{
          text: '全省现存病例占比'
        },
        {
          text: '{name|占比' + '}\n{val|' + (that.list[that.list.length - 1].current * 100 / that.province_list[0].current).toFixed(2) + '%}',
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
                fontSize: 18,
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
          radius: ['35%', '55%'],
          center: ['50%', '53%'],
          avoidLabelOverlap: true,
          label: {
            position: 'outer'
          },
          data: [
            { value: that.list[that.list.length - 1].current, name: '现存病例' },
            { value: that.province_list[0].current - that.list[that.list.length - 1].current, name: '其余地区' }
          ]
        }]
      })
      that.pro_cured_chart.setOption({
        title: {
          text: '全省治愈死亡对比'
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
            type: 'shadow'
          }
        },
        xAxis: {
          type: 'value'
        },
        yAxis: {
          type: 'category',
          data: ['累计死亡', '累计治愈']
        },
        series: [
          {
            name: that.list[0].place,
            type: 'bar',
            data: [that.list[that.list.length - 1].dead, that.list[that.list.length - 1].cured],
            label: {
              show: true,
              position: 'insideRight'
            }
          },
          {
            name: that.province_list[0].place,
            type: 'bar',
            data: [that.province_list[that.province_list.length - 1].dead, that.province_list[that.province_list.length - 1].cured],
            label: {
              show: true,
              position: 'insideRight'
            }
          }
        ]
      })
      that.pro_percent_chart.setOption({
        title: {
          text: '全省比率对比'
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
            type: 'shadow'
          }
        },
        xAxis: {
          type: 'value'
        },
        yAxis: {
          type: 'category',
          data: ['死亡率', '治愈率']
        },
        series: [
          {
            name: that.list[0].place,
            type: 'bar',
            data: [
              (that.list[that.list.length - 1].dead * 100 / that.list[that.list.length - 1].confirm).toFixed(2),
              (that.list[that.list.length - 1].cured * 100 / that.list[that.list.length - 1].confirm).toFixed(2)
            ],
            label: {
              show: true,
              position: 'insideRight'
            }
          },
          {
            name: that.province_list[0].place,
            type: 'bar',
            data: [
              (that.province_list[that.province_list.length - 1].dead * 100 / that.province_list[that.province_list.length - 1].confirm).toFixed(2),
              (that.province_list[that.province_list.length - 1].cured * 100 / that.province_list[that.province_list.length - 1].confirm).toFixed(2)
            ],
            label: {
              show: true,
              position: 'insideRight'
            }
          }
        ]
      })

      echarts.connect([that.confirm_chart, that.cured_chart])
    }
  }
}
</script>

<style scoped>

</style>
