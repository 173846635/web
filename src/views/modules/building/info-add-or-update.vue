<template>
  <el-dialog
    :width="'30%'"
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="80px">
      <el-form-item label="所属小区">
        <org-cascader
          :initOrgValue="orgValue"
          :depth="6"
          @change="orgChange"></org-cascader>
      </el-form-item>
      <el-form-item label="楼幢名称" prop="name">
        <el-input v-model="dataForm.name" placeholder="只需写数字"></el-input>
      </el-form-item>
      <el-form-item label="经纬度" class="latlng">
        <el-col :span="8">
        <el-input v-model.numbe="dataForm.lng" placeholder="经度" onkeydown="if(isNaN(value))execCommand('undo')"
                  onkeyup="if(isNaN(value))execCommand('undo')"
                  onafterpaste="if(isNaN(value))execCommand('undo')" ></el-input>
        </el-col>
        <el-col :span="2" align="center">
          —
        </el-col>
        <el-col :span="8">
        <el-input v-model.numbe="dataForm.lat" placeholder="纬度" onkeydown="if(isNaN(value))execCommand('undo')"
                  onkeyup="if(isNaN(value))execCommand('undo')"
                  onafterpaste="if(isNaN(value))execCommand('undo')"></el-input>
        </el-col>
        <el-col :span="6" align="center">
          <el-button type="info" @click="getLngLat()">获取坐标</el-button>
        </el-col>
      </el-form-item>
      <el-form-item>
        <el-checkbox v-model="dataForm.business">商业楼</el-checkbox>
        <el-checkbox v-model="dataForm.divideUnit">划分单元</el-checkbox>
        <el-checkbox v-model="dataForm.divideHouse">划分房号</el-checkbox>
      </el-form-item>
      <el-form-item label="备注" prop="remark">
        <el-input type="textarea" v-model="dataForm.remark" placeholder="备注"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
    <lng-lat v-if="lnglatVisible" ref="lnglat" @closed="closed"></lng-lat>
  </el-dialog>
</template>

<script>
  import LngLat from '../../../components/lnglat/index'

  export default {
    components: {LngLat},
    data () {
      return {
        lnglatVisible: false,
        visible: false,
        orgValue: [],
        dataForm: {
          id: 0,
          subarea: 0,
          name: '',
          lat: 0,
          lng: 0,
          business: false,
          divideUnit: true,
          divideHouse: true,
          remark: '',
          status: 0
        },
        dataRule: {
          subarea: [
            {required: true, message: '请选择小区', trigger: 'blur'}
          ],
          name: [
            {required: true, message: '楼幢名不能为空', trigger: 'blur'}
          ],
          lat: [
            {required: true, message: '纬度不能为空', trigger: 'blur'}
          ],
          lng: [
            {required: true, message: '经度不能为空', trigger: 'blur'}
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.dataForm.business = false
        this.dataForm.divideHouse = true
        this.dataForm.divideUnit = true
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/building/info/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.orgValue = [data.buildingInfo.province, data.buildingInfo.city, data.buildingInfo.county,
                  data.buildingInfo.street, data.buildingInfo.community, data.buildingInfo.subarea]
                this.dataForm.subarea = data.buildingInfo.subarea
                this.dataForm.name = data.buildingInfo.name
                this.dataForm.lat = data.buildingInfo.lat
                this.dataForm.lng = data.buildingInfo.lng
                this.dataForm.business = data.buildingInfo.business === 1
                this.dataForm.divideUnit = data.buildingInfo.divideUnit === 1
                this.dataForm.divideHouse = data.buildingInfo.divideHouse === 1
                this.dataForm.remark = data.buildingInfo.remark
              }
            })
          }
        })
      },
      // 树形机构选择切换
      orgChange (value) {
        this.dataForm.subarea = value
      },
      // 获取楼幢坐标
      getLngLat () {
        this.lnglatVisible = true
        this.$nextTick(() => {
          this.$refs.lnglat.init(this.dataForm.subarea)
        })
      },
      // 获取坐标窗口关闭回调
      closed (latlng) {
        this.dataForm.lat = latlng.lat
        this.dataForm.lng = latlng.lng
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/building/info/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'subarea': this.dataForm.subarea,
                'name': this.dataForm.name,
                'lat': this.dataForm.lat,
                'lng': this.dataForm.lng,
                'business': this.dataForm.business ? 1 : 0,
                'divideUnit': this.dataForm.divideUnit ? 1 : 0,
                'divideHouse': this.dataForm.divideHouse ? 1 : 0,
                'remark': this.dataForm.remark
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
