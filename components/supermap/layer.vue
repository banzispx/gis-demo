<script>
export default {
  data() {
    return {
      map: {},
      groupList: []
    };
  },
  methods: {
    /**
     * leaflet
     */
    // 生成图层
    init() {
      this.map = this.getMap();
    },
    genTileLayer(mapUrl, props = {}) {
      return L.tilelayer(mapUrl, ...props);
    },
    genTileWMSLayer(mapUrl, props = {}) {
      return L.tilelayer.wms(mapUrl, ...props);
    },
    genImageLayer(mapUrl, props = {}) {
      return L.imageOverlay(mapUrl, ...props);
    },
    genVideoOverlayr(mapUrl, props = {}) {
      return L.videoOverlay(mapUrl, ...props);
    },
    // 添加图层组
    genLayerGroup(groupName, get = true) {
      if (this.groupList.hasOwnProperty(groupName) && this.groupList[groupName]) {
        console.log('已存在同名图层组');
      } else {
        this.groupList[groupName] = L.layerGroup().addTo(this.map);
      }

      if (get) {
        return this.groupList[groupName];
      }
    },
    getLayerGroup(groupName) {
      if (!this.groupList.hasOwnProperty(groupName)) {
        console.log(`不存在名为${groupName}的图层组`);
        return;
      }
      return this.groupList[groupName];
    },
    clearGroup(groupName) {
      if (!this.groupList.hasOwnProperty(groupName)) {
        console.log(`不存在名为${groupName}的图层组`);
        return;
      }

      this.clearLayers(this.groupList[groupName]);
      const { groupList } = this;
      delete groupList[groupName];
      this.groupList = groupList;
    },
    // 移除图层组
    clearLayers(target) {
      if (!target) {
        console.log(`图层为空！`);
        return;
      }
      target.clearLayers();
    },
    // 添加图层到图层组或地图
    addLayer(layer, target) {
      layer.addTo(target);
    },

    /**
     * supermap
     */
    // 生成天地图
    genTiandituTileLayer(option) {
      const { key } = option;
      if (!key) {
        console.log('缺少天地图的应用key,请前往注册: http://lbs.tianditu.gov.cn/api/js4.0/guide.html');
        return;
      }
      return L.supermap.tiandituTileLayer(option);
    },
    // 切换天地图底图
    addTiandituBaseMap(name, mapOption) {
      const { checkOption, genTiandituTileLayer, addLayer, clearLayers, common } = this;
      const { getTiandituKey } = common;

      const mapName = 'basemap';
      if (!checkOption(name, mapOption)) {
        return;
      }

      if (!(this.groupList.hasOwnProperty(mapName) && this.groupList[mapName])) {
        this.groupList[mapName] = L.layerGroup().addTo(this.map);
      }
      clearLayers(this.groupList[mapName]);

      if (Array.isArray(mapOption)) {
        mapOption.forEach(option => {
          addLayer(genTiandituTileLayer({ key: getTiandituKey(), ...option }), this.groupList[mapName]);
        });
      } else {
        addLayer(genTiandituTileLayer({ key: getTiandituKey(), ...mapOption }), this.groupList[mapName]);
      }
    },

    /**
     * common
     */
    // 检查图层配置
    checkOption(name, mapOption) {
      if (!name || !mapOption || this.common.isEmptyObject(mapOption)) {
        console.log('地图名称为空或地图配置为空！');
        return false;
      }
      return true;
    }
  },
  inject: ['getMap']
};
</script>
<template>
</template>