<template>
  <div class="map">
    <div style="width: 100%; height: 100%">
      <button @click="resetMap">返回</button>
      <div ref="map" style="width: 100%; height: 100%"></div>
    </div>
  </div>
</template>

<script>
import world from "@/../static/mapData/world.json";
import china from "@/../static/mapData/china.json";
import anhui from "@/../static/mapData/anhui.json";
import aomen from "@/../static/mapData/aomen.json";
import fujian from "@/../static/mapData/fujian.json";
import gansu from "@/../static/mapData/gansu.json";
import guangdong from "@/../static/mapData/guangdong.json";
import guangxi from "@/../static/mapData/guangxi.json";
import beijing from "@/../static/mapData/beijing.json";
import tianjin from "@/../static/mapData/tianjin.json";
import shanghai from "@/../static/mapData/shanghai.json";
import chongqing from "@/../static/mapData/chongqing.json";
import CQjiangbei from "@/../static/mapData/CQ_jiangbei.json";
import nameMap from "./nameMap";

export default {
  data() {
    return {
      preMap: [],
      curMap: "world", // current map
      curMapData: [], // current map data
      myChart: null,
      jsonMap: {
        // registered maps
        world: world,
        China: china,
        北京: beijing,
        天津: tianjin,
        上海: shanghai,
        重庆: chongqing,
        安徽: anhui,
        澳门: aomen,
        福建: fujian,
        甘肃: gansu,
        广东: guangdong,
        广西: guangxi,
        江北区: CQjiangbei,
      },
      mapData: {
        world: [
          {
            name: "China",
            value: [104.195397, 35.86166, 30089],
          },
        ],
        China: [
          {
            name: "重庆",
            value: [108.380246, 30.807807, 20000],
          },
          {
            name: "北京",
            value: [116.4074, 39.9042, 15000],
          },
          {
            name: "浙江",
            value: [119.7889, 29.1416, 12000],
          },
        ],
        重庆: [],
      },
      nameMap,
    };
  },
  mounted() {
    this.updateMap(this.curMap);
  },
  watch: {
    curMap(newVal) {
      this.updateMap(this.curMap);
    },
  },
  methods: {
    resetMap() {
      this.curMap = "world";
    },
    updateMap(area) {
      // useEffects(, curMap)
      this.curMapData = this.mapData[area];
      this.$echarts.registerMap(area, this.jsonMap[area]);
      this.myChart = this.$echarts.init(this.$refs.map);
      let option = {
        tooltip: {
          trigger: "item",
        },
        visualMap: {
          calculable: true,
          inRange: {
            color: [
              "#313695",
              "#4575b4",
              "#74add1",
              "#abd9e9",
              "#e0f3f8",
              "#ffffbf",
              "#fee090",
              "#fdae61",
              "#f46d43",
              "#d73027",
              "#a50026",
            ],
          },
        },
        geo: {
          map: area,
          show: false,
          roam: false,
          itemStyle: {
            normal: {
              borderColor: "rgba(147, 235, 248, 1)",
              borderWidth: 1.5,
            },
          },
        },
        series: [
          {
            type: "map",
            map: area,
            label: {
              normal: {
                show: false, // 不显示地区名
                textStyle: {
                  color: "#fff",
                  fontSize: 12,
                },
              },
            },
            itemStyle: {
              normal: {
                areaColor: "#061E3D",
                borderColor: "rgba(147, 235, 248, 1)",
                borderWidth: 1.5,
              },
              emphasis: {
                areaColor: {
                  x: 0,
                  y: 0,
                  x2: 0,
                  y2: 1,
                  colorStops: [
                    {
                      offset: 0,
                      color: "#fff", // 0% 处的颜色
                    },
                    {
                      offset: 1,
                      color: "#061E3D", // 100% 处的颜色
                    },
                  ],
                },
                borderColor: "rgba(147, 235, 248, 1)",
                borderWidth: 1.5,
              },
            },
            nameMap: area == "world" ? this.nameMap : {},
          },
          {
            name: "根域名分布",
            type: "effectScatter",
            coordinateSystem: "geo",
            data: this.curMapData,
            encode: {
              value: 2,
            },
            symbolSize: function (val) {
              return val[2] / 800;
            },
            label: {
              formatter: "{b}",
              position: "right",
            },
            itemStyle: {
              color: "#ddb926",
            },
            emphasis: {
              label: {
                show: true,
              },
            },
          },
        ],
      };
      this.myChart.setOption(option, true);
      this.myChart.on("click", (params) => {
        if (this.jsonMap[params.name] && params.seriesType === "effectScatter") {
          if (this.mapData[params.name]) {
            this.curMapData = this.mapData[this.curMap];
            this.curMap = params.name;
          }
        }
      });
      this.myChart.setOption(option, true);
    },
  },
};
</script>

<style scoped>
.map {
  width: 100%;
  height: 100%;
}
</style>
