<template>
  <div class="full">
    <div :id="id" class="map"></div>
    <layer ref="layer" />
    <ui ref="ui" />
    <measure ref="measure" />
    <typhoon ref="typhoon" />
  </div>
</template>
<script>

import layer from './layer';
import ui from './ui';
import measure from './measure';
import typhoon from './typhoon';

export default {
  props: {
    id: {
      type: String,
      default() {
        return String(new Date().valueOf());
      }
    },
    option: {
      type: Object,
      default() {
        return {
          center: [30, 110],
          zoom: 4
        };
      }
    }
  },
  data() {
    return {
      map: {}
    };
  },
  components: { layer, ui, measure, typhoon },
  methods: {
    // 地图初始化
    initSupermap() {
      const { id, option } = this;

      this.map =  L.map(id, option);
      this.$refs.layer.init();
      this.$refs.ui.init();
      this.$refs.measure.init();
      this.$refs.typhoon.init();
    },
    getMapId() {
      return this.id;
    },
    getMap() {
      return this.map;
    },
    getLayer() {
      return this.$refs.layer;
    },
    getUI() {
      return this.$refs.ui;
    },
    // 设置地图初始配置 center zoom id mapUrl
    changeOption(key, value) {
      if (!this.initOption.list.includes(key)) {
        console.log(`地图初始化设置${key}失败！`);
        return;
      }
      this.initOption[key] = value;
      console.log(this.initOption);
    }
  },
  mounted() {
    // check the resourse L.supermap -> plugin/common.js
    this.common.checkLoading('L.supermap');
    this.initSupermap();
    this.layer = this.$refs.layer;
    this.ui = this.$refs.ui;

    this.common.checkLoading('turf');
    this.measure = this.$refs.measure;
    this.typhoon = this.$refs.typhoon;
  },
  provide: function () {
    return {
      getMap: this.getMap,
      getLayer: this.getLayer,
      getUI: this.getUI,
      getMapId: this.getMapId
    }
  }
};
</script>
<style scoped>
  .map	{
    /* position: absolute; */
    margin-top: 0;
    width: 100%;
    height: 100%;
    z-index: 0;
    background-size: 100% 100%;
    background-color: #004a77;
  }
  .full {
    width: 100%;
    height: 100%;
  }
</style>