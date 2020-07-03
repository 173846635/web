<template>
  <el-cascader
    v-model="orgValue"
    :options="orgOptions"
    :show-all-levels="false"
    :props="{checkStrictly: getCheckStrictly}"
    @change="orgChange"></el-cascader>
</template>

<script>
  export default {
    name: 'org-cascader',
    data () {
      return {
        // 当前选中的机构的父子id数组
        orgValue: [],
        // 所有组织机构的父子选项数组
        orgOptions: []
      }
    },
    props: {
      /**
       * 初始的组织机构选定，需要从顶级父级id到选定机构id组成的数组
       * 如果不设置，则默认选定用户的最高权限
       */
      initOrgValue: {
        type: Array
      },
      /**
       * 级联选择到组织机构的第几级
       */
      depth: {
        type: Number,
        required: true
      },
      /**
       * 是否允许选择任一级别的组织机构
       * 默认为否
       */
      checkStrictly: {
        type: Boolean
      }
    },
    computed: {
      getCheckStrictly () {
        if (this.checkStrictly === undefined) {
          return false
        } else {
          return this.checkStrictly
        }
      }
    },
    created () {
      this.getOrgs()
    },
    methods: {
      // 获取用户的树形权限机构
      getOrgs () {
        this.$nextTick(() => {
          this.$http({
            url: this.$http.adornUrl(`/sys/userdataauth/getUserOrg`),
            method: 'get',
            params: this.$http.adornParams({
              'depth': this.depth
            })
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.orgOptions = data.userTreeOrg
              // 给机构赋初值，默认为第一个
              if (data.userTreeOrg.length > 0) {
                if (this.initOrgValue == null || this.initOrgValue === undefined || this.initOrgValue.length === 0) {
                  this.orgValue = [data.userTreeOrg[0].value]
                } else {
                  this.orgValue = this.initOrgValue
                }
                console.log('org.vue:' + this.orgValue)
                this.$emit('change', this.orgValue[this.orgValue.length - 1])
              }
            }
          })
        })
      },
      // btn () {
      //   this.cascaderValue = ""; //清空v-model
      //   ++this.isResouceShow ; //key值自增
      //   this.options_cascader = []; //清空内容
      // },
      // 树形机构选择切换
      orgChange (value) {
        // 选择返回的是一个父子id的数组
        if (value.length > 0) {
          this.$emit('change', this.orgValue[this.orgValue.length - 1])
        }
      }
    }
  }
</script>

<style scoped>

</style>
