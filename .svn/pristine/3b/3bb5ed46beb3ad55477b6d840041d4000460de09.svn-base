<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item label="机构名称：">
        <org-cascader
          :depth="6"
          :checkStrictly="true"
          @change="orgChange"></org-cascader>
      </el-form-item>
      <el-form-item label="楼幢名称：">
        <el-input v-model="dataForm.name" placeholder="楼幢名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('building:info:save')" type="primary" @click="addOrUpdateHandle()">新增
        </el-button>
        <el-button v-if="isAuth('building:info:delete')" type="danger" @click="deleteHandle()"
                   :disabled="dataListSelections.length <= 0">批量删除
        </el-button>
        <el-button v-if="isAuth('building:info:save')" type="success" @click="importHandle()">新增
        </el-button>
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
        prop="communityName"
        header-align="center"
        align="center"
        label="社区">
      </el-table-column>
      <el-table-column
        prop="subareaName"
        header-align="center"
        align="center"
        label="小区">
      </el-table-column>
      <el-table-column
        prop="name"
        header-align="center"
        align="center"
        label="楼幢名">
      </el-table-column>
      <el-table-column
        prop="business"
        header-align="center"
        align="center"
        :formatter="dataFormatter"
        label="是否商业楼">
      </el-table-column>
      <el-table-column
        prop="divideUnit"
        header-align="center"
        align="center"
        :formatter="dataFormatter"
        label="是否划分单元">
      </el-table-column>
      <el-table-column
        prop="divideHouse"
        header-align="center"
        align="center"
        :formatter="dataFormatter"
        label="是否划分房号">
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        label="创建时间">
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
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    <file-upload v-if="fileUploadVisible" ref="fileUpload" :accept="'.xls,.xlsx'" :action="'/building/info/import'"
                 :show-result="true" :template-url="'/building/info/import'" :limit="1"
                 :tip="'只能上传excel文件，且不超过10M'"></file-upload>
  </div>
</template>

<script>
  import AddOrUpdate from './info-add-or-update'
  import FileUpload from '../../../components/upload/file'

  export default {
    data () {
      return {
        dataForm: {
          orgId: 0,
          name: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        init: true,
        fileUploadVisible: false
      }
    },
    components: {
      FileUpload,
      AddOrUpdate
    },
    activated () {
      // this.getDataList()
    },
    methods: {
      // 树形机构选择切换
      orgChange (value) {
        this.dataForm.orgId = value
        if (this.init) {
          this.init = false
          this.getDataList()
        }
      },
      // 数据格式化
      dataFormatter (row, column) {
        if ((row['business'] === 1 && column.property === 'business') ||
        (row['divideUnit'] === 1 && column.property === 'divideUnit') ||
        (row['divideHouse'] === 1 && column.property === 'divideHouse')) {
          return '是'
        } else {
          return '否'
        }
      },
      // 批量导入前处理
      importHandle () {
        this.fileUploadVisible = true
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/building/info/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'orgId': this.dataForm.orgId,
            'name': this.dataForm.name
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
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
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/building/info/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      }
    }
  }
</script>
