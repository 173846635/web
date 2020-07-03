<template>
  <el-dialog
    :width="'50%'"
    :title="'坐标拾取器'"
    :close-on-click-modal="false"
    append-to-body
    @opened="initMap()"
    :custom-class="'mapDialog'"
    :visible.sync="visible">
    <div id="map" ref="myMap" style="position: relative;">
      <el-input v-model="search" placeholder="请输入搜索的内容" @change="mapSearch()"
                style="position: absolute;right:0;top:0;z-index: 1000;width: 200px;margin-right: 1px;"/>
    </div>
  </el-dialog>
</template>

<script>
  import L from 'leaflet'
  // eslint-disable-next-line no-unused-vars
  import chinaProvider from 'leaflet.chinesetmsproviders'
  import 'leaflet/dist/leaflet.css'

  var gaodeGeoCodeAPI = 'https://restapi.amap.com/v3/place/text'
  var gaodeAPIKey = '83d81b7a2929edbfe46417c8d045c9fe'

  export default {
    name: 'LngLat',
    data () {
      return {
        orgId: 0,
        visible: false,
        lng: 120.1489,
        lat: 30.2927,
        depth: 1,
        map: null,
        search: ''
      }
    },
    methods: {
      // 机构id，初始化时需要以哪个组织机构的坐标为中心
      init (orgId) {
        this.visible = true
        this.orgId = orgId
        this.getOrg()
      },
      // 获取当前机构的坐标点作为地图的中心点
      getOrg () {
        this.$http({
          url: this.$http.adornUrl('/sys/org/info/' + this.orgId),
          method: 'get',
          data: {}
        }).then(({data}) => {
          if (data && data.code === 0) {
            if (data.sysOrg.lng > 0) {
              this.lng = data.sysOrg.lng
              this.lat = data.sysOrg.lat
            }
            this.depth = data.sysOrg.depth
          } else {
            this.$message.error(data.msg)
          }
        })
      },
      // 地图初始化
      initMap () {
        if (!this.map) {
          // 根据机构的层级，确定地图的放大级别
          var zoom = 18
          if (this.depth === 1) {
            zoom = 12
          } else if (this.depth === 2) {
            zoom = 14
          } else if (this.depth === 3) {
            zoom = 16
          }

          let map = L.map('map', {
            minZoom: 12,
            maxZoom: 18,
            center: [this.lat, this.lng],
            zoom: zoom
          })

          L.tileLayer.chinaProvider('GaoDe.Normal.Map', {
            maxZoom: 18,
            minZoom: 12
          }).addTo(map)

          this.map = map

          // this和dblclick中的this有冲突
          var that = this
          map.off('dblclick').on('dblclick', function (e) {
            // 双击得到坐标，关闭当前dialog
            that.closed({lng: e.latlng.lng.toFixed(7), lat: e.latlng.lat.toFixed(7)})
          })
        }
      },
      /**
       * 地图搜索
       */
      mapSearch () {
        if (this.map != null) {
          // 根据高德api搜索
          this.$jsonp(gaodeGeoCodeAPI + '?key=' + gaodeAPIKey + '&keywords=' + this.search).then(json => {
            if (parseInt(json.status) === 1) {
              if (json.pois.length > 0) {
                var location = json.pois[0].location
                var lnglat = location.split(',')
                this.map.panTo(new L.LatLng(lnglat[1], lnglat[0]))
              }
            }
          })
        }
      },
      // 窗口关闭
      closed (latlng) {
        this.visible = false
        // 回调父组件的方法
        this.$emit('closed', latlng)
      }
    }
  }
</script>

<style>
  #map {
    width:100%;
    height:600px;
  }
  .mapDialog .el-dialog__body{
    padding: 0 !important;
  }
</style>
