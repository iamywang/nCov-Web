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
      </el-card>
    </el-col>
    <el-col :span="16">
      <el-card style="height: 260px; margin: 8px">
        <el-tag>疫情科普</el-tag>
      </el-card>
      <el-card style="height: 400px; margin: 8px">
        <el-tag>疫情词云</el-tag>
      </el-card>
    </el-col>
  </div>
</template>

<script>
export default {

}
</script>

<style scoped>

</style>
