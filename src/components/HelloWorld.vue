<script>
import mapboxgl from "mapbox-gl";
import { MapboxLayer } from "@deck.gl/mapbox";
import { Tile3DLayer } from "@deck.gl/geo-layers";
import { Tiles3DLoader } from "@loaders.gl/3d-tiles";

mapboxgl.accessToken =
  "pk.eyJ1IjoiaW4ydHdhbiIsImEiOiJja3l6bjJ0ZWIwY2d0Mm5yemZ3NWFmOWhjIn0._B39LsH99PmOk74938_tUg";

const nanXun =
  "https://visom-gis.oss-cn-beijing.aliyuncs.com/3dtiles/%E5%8D%97%E6%B5%94%E6%A8%A1%E5%9E%8B1/%E5%8C%BA%E5%9D%971-%E4%BF%AE%E6%B0%B4%E9%9D%A2-3d%20tiles/Production_2/Scene/Production_2.json";
const yunYang = "https://yunkaigaosu.cn:1010/data/tilesets/bridge/tileset.json";
export default {
  data() {
    return {
      msg: "Hello Vue 3 + Vite + mapbox",
    };
  },
  components: {},
  watch: {
    selected(n) {
      this.changeAddNodeType(n);
    },
  },
  mounted() {
    const zoom = 14;
    const nanXunLat = 30.885761605180775;
    const nanXunLng = 120.41632822243005;

    const yunYangAdderss = [108.737581, 30.983239];

    const tile3dLayer = new MapboxLayer({
      id: "tile3dlayer",
      type: Tile3DLayer,
      pointSize: 1,
      data: yunYang,
      // data: nanXun,
      // opacity: 0.9,
      pickable: true,
      loader: Tiles3DLoader,
      onTileLoad: (tileHeader) => {
        tileHeader.content.cartographicOrigin.z -= 200; //  z轴偏移
        console.log(tileHeader)
        },
      // onTileLoad: (tileHeader) => console.log(tileHeader),
      onHover: (Tile3DLayer, event) =>
        console.log("Hovered:", Tile3DLayer, event),
    });

    const map = new mapboxgl.Map({
      container: "map", // container ID
      style: 'mapbox://styles/mapbox-map-design/ckhqrf2tz0dt119ny6azh975y',
      // center: [nanXunLng, nanXunLat],
      center: yunYangAdderss,
      zoom: zoom,
    });

    map.on("load", () => {
      map.addSource("mapbox-dem", {
        type: "raster-dem",
        url: "mapbox://mapbox.mapbox-terrain-dem-v1",
        tileSize: 512,
        maxzoom: 14,
      });
      // add the DEM source as a terrain layer with exaggerated height
      map.setTerrain({ source: "mapbox-dem", exaggeration: 1 });

      // add a sky layer that will show when the map is highly pitched
      map.addLayer({
        id: "sky",
        type: "sky",
        paint: {
          "sky-type": "atmosphere",
          "sky-atmosphere-sun": [0.0, 0.0],
          "sky-atmosphere-sun-intensity": 15,
        },
      });
      map.addLayer(tile3dLayer);
      console.log("load");
    });
  },
  methods: {},
};
</script>

<template>
  <h1>{{ msg }}</h1>

  <div id="map"></div>
</template>

<style scoped>
a {
  color: #42b983;
}
#map {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
}
</style>
