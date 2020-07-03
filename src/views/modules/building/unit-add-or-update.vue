<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="楼幢id" prop="buildingId">
      <el-input v-model="dataForm.buildingId" placeholder="楼幢id"></el-input>
    </el-form-item>
    <el-form-item label="单元名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="单元名称"></el-input>
    </el-form-item>
    <el-form-item label="" prop="floors">
      <el-input v-model="dataForm.floors" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="remark">
      <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态"></el-input>
    </el-form-item>
    <el-form-item label="创建者" prop="createUserId">
      <el-input v-model="dataForm.createUserId" placeholder="创建者"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="修改者" prop="updateUserId">
      <el-input v-model="dataForm.updateUserId" placeholder="修改者"></el-input>
    </el-form-item>
    <el-form-item label="修改时间" prop="updateTime">
      <el-input v-model="dataForm.updateTime" placeholder="修改时间"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          buildingId: '',
          name: '',
          floors: '',
          remark: '',
          status: '',
          createUserId: '',
          createTime: '',
          updateUserId: '',
          updateTime: ''
        },
        dataRule: {
          buildingId: [
            { required: true, message: '楼幢id不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '单元名称不能为空', trigger: 'blur' }
          ],
          floors: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态  0正常 1删除不能为空', trigger: 'blur' }
          ],
          createUserId: [
            { required: true, message: '创建者不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          updateUserId: [
            { required: true, message: '修改者不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '修改时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/building/unit/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.buildingId = data.buildingUnit.buildingId
                this.dataForm.name = data.buildingUnit.name
                this.dataForm.floors = data.buildingUnit.floors
                this.dataForm.remark = data.buildingUnit.remark
                this.dataForm.status = data.buildingUnit.status
                this.dataForm.createUserId = data.buildingUnit.createUserId
                this.dataForm.createTime = data.buildingUnit.createTime
                this.dataForm.updateUserId = data.buildingUnit.updateUserId
                this.dataForm.updateTime = data.buildingUnit.updateTime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/building/unit/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'buildingId': this.dataForm.buildingId,
                'name': this.dataForm.name,
                'floors': this.dataForm.floors,
                'remark': this.dataForm.remark,
                'status': this.dataForm.status,
                'createUserId': this.dataForm.createUserId,
                'createTime': this.dataForm.createTime,
                'updateUserId': this.dataForm.updateUserId,
                'updateTime': this.dataForm.updateTime
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
