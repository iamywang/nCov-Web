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
            <div style="font-size: 18px">{{ list[0].place }}（{{ list[0].day }}）</div>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>累计确诊</el-tag>
          </el-row>
          <el-row style="margin-bottom: 8px">
            <div style="font-size: 18px">{{ list[0].confirm }}（新增{{ list[0].new_confirm }}）</div>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>现存确诊</el-tag>
          </el-row>
          <el-row style="margin-bottom: 8px">
            <div style="font-size: 18px">{{ list[0].current }}</div>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>累计治愈</el-tag>
            <span>{{ list[0].cured }}（新增{{ list[0].new_cured }}）</span>
          </el-row>
          <el-row style="margin-bottom: 8px">
            <el-tag>治愈率</el-tag>
            <span>{{ (list[0].cured * 100 /list[0].confirm).toFixed(2) }}%</span>
          </el-row>
        </el-col>
        <el-col :span="5">
          <el-row style="margin-bottom: 8px">
            <el-tag>累计死亡</el-tag>
            <span>{{ list[0].dead }}（新增{{ list[0].new_dead }}）</span>
          </el-row>
          <el-row>
            <el-tag style="margin-bottom: 8px">死亡率</el-tag>
            <span>{{ (list[0].dead * 100 /list[0].confirm).toFixed(2) }}%</span>
          </el-row>
        </el-col>
      </el-card>
    </el-col>
    <el-col :span="9" style="margin: 8px">
      <el-card style="height: 300px">
        <el-row>确诊病例趋势</el-row>
      </el-card>
    </el-col>
    <el-col :span="9" style="margin: 8px">
      <el-card style="height: 300px">
        <el-row>治愈和死亡病例趋势</el-row>
      </el-card>
    </el-col>
    <el-col :span="5" style="margin: 8px">
      <el-card style="height: 300px">
        <el-row>确诊省内分布</el-row>
      </el-card>
    </el-col>
    <el-table :data="list" element-loading-text="正在查询" stripe border fit highlight-current-row>
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
export default {
  data() {
    return {
      list: [
        { 'day': '2020-05-12', 'confirm': 84451, 'current': 237, 'cured': 79570, 'new_confirm': 1, 'new_cured': 37, 'new_dead': 1, 'dead': 4644, 'place': '全国' }
      ],
      place_to_search: '',
      place_to_search_mul: '',
      date_to_search: '',
      date_to_search_st: '',
      date_to_search_ed: ''
    }
  },
  methods: {
    searchFunc_1() {
      var that = this
      this.listLoading = true
      axios.get('/server/getProvince/', {
        params: {
          day: that.date_to_search,
          province: that.place_to_search
        }
      }).then(function(res) {
        that.list = res.data
      })
    },
    searchFunc_1_ex() {
      var that = this
      this.listLoading = true
      axios.get('/server/getCity/', {
        params: {
          day: that.date_to_search,
          city: that.place_to_search
        }
      }).then(function(res) {
        that.list = res.data
      })
    },
    searchFunc_2() {
      var that = this
      this.listLoading = true
      axios.get('/server/getProvinceSeries/', {
        params: {
          start: that.date_to_search_st,
          end: that.date_to_search_ed,
          province: that.place_to_search_mul
        }
      }).then(function(res) {
        that.list = res.data
      })
    },
    searchFunc_2_ex() {
      var that = this
      this.listLoading = true
      axios.get('/server/getCitySeries/', {
        params: {
          start: that.date_to_search_st,
          end: that.date_to_search_ed,
          city: that.place_to_search_mul
        }
      }).then(function(res) {
        that.list = res.data
      })
    }
  }
}
</script>

<style scoped>

</style>
