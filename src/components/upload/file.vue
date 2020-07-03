<template>
  <el-dialog>
    <el-form :model="form">
      <el-form-item label="上传文件" :label-width="formLabelWidth" v-show="!hiddenUpload">
        <el-upload
          ref="uploadExcel"
          multiple
          action="action"
          :limit="getLimit"
          :auto-upload="false"
          accept="getAccept"
          :before-upload="beforeUploadFile"
          :on-change="fileChange"
          :on-exceed="exceedFile"
          :on-success="handleSuccess"
          :on-error="handleError"
          :file-list="fileList">
          <el-button size="small" plain>选择文件</el-button>
          <div slot="tip" class="el-upload__tip" v-if="tip != ''">{{tip}}</div>
        </el-upload>
      </el-form-item>
      <el-form-item v-if="!hiddenResult">
        <el-table
          :data="dataList"
          border
          style="width: 100%;">
          <el-table-column
            prop="num"
            header-align="center"
            align="center"
            label="序号">
          </el-table-column>
          <el-table-column
            prop="content"
            header-align="center"
            align="center"
            label="导入内容">
          </el-table-column>
          <el-table-column
            prop="errorMsg"
            header-align="center"
            align="center"
            label="错误信息">
          </el-table-column>
        </el-table>
      </el-form-item>
      <el-form-item>
        <el-link v-if="!hiddenErrorFileUrl" :href="errorFileUrl">模板下载</el-link>
        <el-link :href="templateUrl">模板下载</el-link>
        <el-button size="small" type="primary" @click="uploadFile">立即上传</el-button>
        <el-button size="small">取消</el-button>
      </el-form-item>
    </el-form>
  </el-dialog>
</template>

<script>
  export default {
    name: 'file-upload',
    data () {
      return {
        // 文件上传数
        limitNum: 1,
        formLabelWidth: '80px',
        form: {
          file: ''
        },
        // 选中文件数组
        fileList: [],
        // 是否隐藏上传组件
        hiddenUpload: false,
        // 返回的错误记录列表
        dataList: [],
        // 是否隐藏错误结果
        hiddenResult: true,
        // 导入错误内容下载地址
        errorFileUrl: '',
        // 显示错误内容下载地址
        hiddenErrorFileUrl: true
      }
    },
    props: {
      /**
       * 上传的post地址
       */
      action: {
        type: String,
        required: true
      },
      /**
       * 模板下载路径
       */
      templateUrl: {
        type: String
      },
      /**
       * 上传文件数，默认为1
       */
      limit: {
        type: Number
      },
      /**
       * 上传文件的限制类型
       */
      accept: {
        type: String
      },
      /**
       * 上传文件格式提醒
       */
      tip: {
        type: String
      },
      /**
       * 是否需要显示上传的结果
       */
      showResult: {
        type: Boolean
      },
      /**
       * 是否需要下载上传错误的原始记录
       */
      needDownloadError: {
        type: Boolean
      }
    },
    computed: {
      getLimit () {
        if (this.limit === undefined) {
          return 1
        } else {
          return this.limit
        }
      },
      getAccept () {
        if (this.accept === undefined) {
          return ''
        } else {
          return this.accept
        }
      },
      getShowResult () {
        if (this.showResult === undefined) {
          return true
        } else {
          return this.showResult
        }
      },
      getNeedDownloadError () {
        if (this.needDownloadError === undefined) {
          return true
        } else {
          return this.needDownloadError
        }
      }
    },
    methods: {
      // 文件超出个数限制时的钩子
      exceedFile (files, fileList) {
        this.$notify.warning({
          title: '警告',
          message: `只能选择 ${this.getLimit()} 个文件，当前共选择了 ${files.length + fileList.length} 个`
        })
      },
      // 文件状态改变时的钩子
      fileChange (file, fileList) {
        console.log('change')
        console.log(file)
        this.form.file = file.raw
        console.log(this.form.file)
        console.log(fileList)
      },
      // 上传文件之前的钩子, 参数为上传的文件,若返回 false 或者返回 Promise 且被 reject，则停止上传
      beforeUploadFile (file) {
        console.log('before upload')
        console.log(file)
        // let extension = file.name.substring(file.name.lastIndexOf('.') + 1)
        let size = file.size / 1024 / 1024
        // if (extension !== 'xlsx') {
        //   this.$notify.warning({
        //     title: '警告',
        //     message: `只能上传Excel2017（即后缀是.xlsx）的文件`
        //   })
        // }
        if (size > 10) {
          this.$notify.warning({
            title: '警告',
            message: `文件大小不得超过10M`
          })
        }
      },
      // 文件上传成功时的钩子
      handleSuccess (res, file, fileList) {
        this.$notify.success({
          title: '成功',
          message: `文件上传成功`
        })
        if (this.getShowResult()) {
          this.dataList = res.errorData
          this.hiddenResult = false
          this.hiddenUpload = true
          if (this.getNeedDownloadError()) {
            this.errorFileUrl = res.errorFileUrl
            this.hiddenErrorFileUrl = false
          }
        }
      },
      // 文件上传失败时的钩子
      // eslint-disable-next-line handle-callback-err
      handleError (err, file, fileList) {
        this.$notify.error({
          title: '错误',
          message: `文件上传失败`
        })
      },
      uploadFile () {
        this.$refs.uploadExcel.submit()
      }
    }
  }
</script>

<style scoped>

</style>
