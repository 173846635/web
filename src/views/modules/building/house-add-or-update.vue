<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="省" prop="province">
      <el-input v-model="dataForm.province" placeholder="省"></el-input>
    </el-form-item>
    <el-form-item label="市" prop="city">
      <el-input v-model="dataForm.city" placeholder="市"></el-input>
    </el-form-item>
    <el-form-item label="县" prop="county">
      <el-input v-model="dataForm.county" placeholder="县"></el-input>
    </el-form-item>
    <el-form-item label="街道" prop="street">
      <el-input v-model="dataForm.street" placeholder="街道"></el-input>
    </el-form-item>
    <el-form-item label="社区id" prop="community">
      <el-input v-model="dataForm.community" placeholder="社区id"></el-input>
    </el-form-item>
    <el-form-item label="小区id" prop="subarea">
      <el-input v-model="dataForm.subarea" placeholder="小区id"></el-input>
    </el-form-item>
    <el-form-item label="楼幢id" prop="buildingId">
      <el-input v-model="dataForm.buildingId" placeholder="楼幢id"></el-input>
    </el-form-item>
    <el-form-item label="单元id" prop="unitId">
      <el-input v-model="dataForm.unitId" placeholder="单元id"></el-input>
    </el-form-item>
    <el-form-item label="房屋名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="房屋名称 例:501"></el-input>
    </el-form-item>
    <el-form-item label="完整名称" prop="fullName">
      <el-input v-model="dataForm.fullName" placeholder="完整名称例:1幢3单元501"></el-input>
    </el-form-item>
    <el-form-item label="面积" prop="areas">
      <el-input v-model="dataForm.areas" placeholder="面积"></el-input>
    </el-form-item>
    <el-form-item label="所在楼层" prop="floor">
      <el-input v-model="dataForm.floor" placeholder="所在楼层"></el-input>
    </el-form-item>
    <el-form-item label="使用类型" prop="useType">
      <el-input v-model="dataForm.useType" placeholder="使用类型：0未知   1自住   2商用"></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="remark">
      <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态  0正常 1删除"></el-input>
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
          province: '',
          city: '',
          county: '',
          street: '',
          community: '',
          subarea: '',
          buildingId: '',
          unitId: '',
          name: '',
          fullName: '',
          areas: '',
          floor: '',
          useType: '',
          remark: '',
          status: '',
          createUserId: '',
          createTime: '',
          updateUserId: '',
          updateTime: ''
        },
        dataRule: {
          province: [
            { required: true, message: '省不能为空', trigger: 'blur' }
          ],
          city: [
            { required: true, message: '市不能为空', trigger: 'blur' }
          ],
          county: [
            { required: true, message: '县不能为空', trigger: 'blur' }
          ],
          street: [
            { required: true, message: '街道不能为空', trigger: 'blur' }
          ],
          community: [
            { required: true, message: '社区id不能为空', trigger: 'blur' }
          ],
          subarea: [
            { required: true, message: '小区id不能为空', trigger: 'blur' }
          ],
          buildingId: [
            { required: true, message: '楼幢id不能为空', trigger: 'blur' }
          ],
          unitId: [
            { required: true, message: '单元id不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '房屋名称 例:501 不能为空', trigger: 'blur' }
          ],
          fullName: [
            { required: true, message: '完整名称例:1幢3单元501 不能为空', trigger: 'blur' }
          ],
          areas: [
            { required: true, message: '面积不能为空', trigger: 'blur' }
          ],
          floor: [
            { required: true, message: '所在楼层不能为空', trigger: 'blur' }
          ],
          useType: [
            { required: true, message: '使用类型：0未知   1自住   2商用 不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态  0正常 1删除 不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/building/house/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.province = data.buildingHouse.province
                this.dataForm.city = data.buildingHouse.city
                this.dataForm.county = data.buildingHouse.county
                this.dataForm.street = data.buildingHouse.street
                this.dataForm.community = data.buildingHouse.community
                this.dataForm.subarea = data.buildingHouse.subarea
                this.dataForm.buildingId = data.buildingHouse.buildingId
                this.dataForm.unitId = data.buildingHouse.unitId
                this.dataForm.name = data.buildingHouse.name
                this.dataForm.fullName = data.buildingHouse.fullName
                this.dataForm.areas = data.buildingHouse.areas
                this.dataForm.floor = data.buildingHouse.floor
                this.dataForm.useType = data.buildingHouse.useType
                this.dataForm.remark = data.buildingHouse.remark
                this.dataForm.status = data.buildingHouse.status
                this.dataForm.createUserId = data.buildingHouse.createUserId
                this.dataForm.createTime = data.buildingHouse.createTime
                this.dataForm.updateUserId = data.buildingHouse.updateUserId
                this.dataForm.updateTime = data.buildingHouse.updateTime
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
              url: this.$http.adornUrl(`/building/house/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'province': this.dataForm.province,
                'city': this.dataForm.city,
                'county': this.dataForm.county,
                'street': this.dataForm.street,
                'community': this.dataForm.community,
                'subarea': this.dataForm.subarea,
                'buildingId': this.dataForm.buildingId,
                'unitId': this.dataForm.unitId,
                'name': this.dataForm.name,
                'fullName': this.dataForm.fullName,
                'areas': this.dataForm.areas,
                'floor': this.dataForm.floor,
                'useType': this.dataForm.useType,
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
