<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item label="国家/地区" prop="countryId">
        <el-select v-model.trim="dataForm.countryId" clearable placeholder="国家/地区" filterable>
          <el-option
            v-for="item in countryEntities"
            :key="item.id"
            :label="item.cn"
            :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="关键词">
        <el-input v-model="dataForm.key" placeholder="关键词" clearable></el-input>
      </el-form-item>
      <el-form-item label="数量" prop="num">
        <el-select v-model.trim="dataForm.num" clearable placeholder="数量" filterable>
          <el-option
            v-for="item in nums"
            :key="item.value"
            :label="item.value"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button @click="createSearchProcess()">搜索</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="id">
      </el-table-column>
      <el-table-column
        prop="companyName"
        header-align="center"
        align="center"
        label="公司名称">
      </el-table-column>
      <el-table-column
        prop="employeeNum"
        header-align="center"
        align="center"
        label="员工人数">
      </el-table-column>
      <el-table-column
        prop="code"
        header-align="center"
        align="center"
        label="">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->

  </div>
</template>

<script>
import Vue from 'vue'

export default {
  data () {
    return {
      dataForm: {
        key: '',
        countryId: '',
        num: '',
        taskId: ''
      },
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
      countryEntities: [],
      nums: [
        {
          value: 10
        },
        {
          value: 30
        },
        {
          value: 50
        },
        {
          value: 100
        }
      ]
    }
  },
  components: {},
  activated () {
    this.countryList()
  },
  methods: {
    // 获取数据列表
    getDataList () {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/search/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.pageIndex,
          'key': this.dataForm.key,
          'countryId': this.dataForm.countryId,
          'num': this.dataForm.num,
          'token': Vue.cookie.get('token')
        })
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list
          this.totalPage = data.page.total
        } else {
          this.dataList = []
          this.totalPage = 0
        }
        this.dataListLoading = false
      })
    },
    // 每页数
    sizeChangeHandle (val) {
      this.pageSize = val
      this.pageIndex = 1
      this.getDataList()
    },
    // 当前页
    currentChangeHandle (val) {
      this.pageIndex = val
      this.getDataList()
    },
    // 多选
    selectionChangeHandle (val) {
      this.dataListSelections = val
    },
    countryList () {
      this.$http({
        url: this.$http.adornUrl(`/commonData/countryList`),
        method: 'get'
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.countryEntities = data.list
        }
      })
    },
    createSearchProcess () {
      this.pageIndex = 1
      this.$http({
        url: this.$http.adornUrl('/search/createSearchProcess'),
        method: 'get',
        params: this.$http.adornParams({
          'key': this.dataForm.key,
          'countryId': this.dataForm.countryId,
          'num': this.dataForm.num,
          'token': Vue.cookie.get('token')
        })
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.dataForm.taskId = data.searchRecordEntity.id
          this.getDataList()
        } else {

        }
      })
    }
  }
}
</script>
