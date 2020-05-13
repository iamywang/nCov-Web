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
    <el-col :span="24" style="margin: 8px">
      <el-card style="height: 100px">
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
    <el-col :span="9" style="margin: 8px">
      <el-card style="height: 400px">
        <div id="confirm_chart" :style="{width: '100%', height: '350px'}" />
      </el-card>
    </el-col>
    <el-col :span="9" style="margin: 8px">
      <el-card style="height: 400px">
        <div id="cured_chart" :style="{width: '100%', height: '350px'}" />
      </el-card>
    </el-col>
    <el-col :span="5" style="margin: 8px">
      <el-card style="height: 400px">
        <el-row>省内分布情况</el-row>
        <el-tag>累计确诊-饼状图</el-tag>
        <div>{{ list[list.length - 1].confirm }}/{{ province_list[0].confirm }}</div>
        <el-tag>现存确诊-饼状图</el-tag>
        <div>{{ list[list.length - 1].current }}/{{ province_list[0].current }}</div>
        <el-tag>累计治愈-条形图</el-tag>
        <div>{{ list[list.length - 1].cured }}/{{ province_list[0].cured }}</div>
        <el-tag>累计死亡-条形图</el-tag>
        <div>{{ list[list.length - 1].dead }}/{{ province_list[0].dead }}</div>
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

export default {
  data() {
    return {
      list: [
        { 'day': '2020-05-12', 'confirm': 84451, 'current': 237, 'cured': 79570, 'new_confirm': 1, 'new_cured': 37, 'new_dead': 1, 'dead': 4644, 'place': '全国' }
      ],
      province_list: [],
      last_14: [],
      place_to_search: '',
      place_to_search_mul: '',
      date_to_search: '',
      date_to_search_st: '',
      date_to_search_ed: '',
      confirm_chart: null,
      cured_chart: null
    }
  },
  created() {
    var that = this
    that.last_14 = that.list
    that.province_list = that.list
  },
  mounted() {
    var that = this
    that.confirm_chart = echarts.init(document.getElementById('confirm_chart'))
    that.cured_chart = echarts.init(document.getElementById('cured_chart'))
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
        },
        {
          name: '现存确诊',
          smooth: true,
          type: 'line',
          data: last_current_data
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
        },
        {
          name: '累计死亡',
          smooth: true,
          type: 'line',
          data: last_dead_data
        }]
      })
    }
  }
}
</script>

<style scoped>

</style>
