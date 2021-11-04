<template>
  <div class="map">
    <h3>Карта офиса</h3>

    <div v-if="!isLoading" class="map-root">
      <MapSVG ref="svg" />
      <Table v-show="false" ref="table" />
    </div>
    <div v-else>Loading...</div>
  </div>
</template>

<script>
import MapSVG from "../assets/images/map.svg";
import Table from "../assets/images/workPlace.svg";
import * as d3 from "d3";
import tables from "../assets/data/tables.json";
import legend from "../assets/data/legend.json";

export default {
  data() {
    return {
      isLoading: false,
      svg: null,
      g: null,
      tables: [],
      legend: [],
      tableSVG: null,
    };
  },
  components: {
    MapSVG,
    Table,
  },
  created() {
    this.tables = tables;
    this.legend = legend;
  },
  mounted() {
    this.isLoading = true;
    this.svg = d3.select(this.$refs.svg);
    this.g = this.svg.select("g");
    this.tableSVG = d3.select(this.$refs.table);

    if (this.g) {
      this.drawTables();
    } else {
      console.log("SVG not find <g>");
    }

    this.isLoading = false;
  },
  methods: {
    handlerClick(table_id) {
      this.$emit("tableClick", table_id);
    },
    drawTables() {
      const svgTablesGroupPalce = this.g
        .append("g")
        .classed("groupPalces", true);
      this.tables.map((table) => {
        const targetSeat = svgTablesGroupPalce
          .append("g")
          .attr("transform", `translate(${table.x} , ${table.y})  scale(0.5)`)
          .attr("id", table.id)
          .classed("employer-place", true);
        targetSeat
          .append("g")
          .attr("transform", `rotate(${table.rotate || 0})`)
          .attr("group_id", table.group_id)
          .classed("table", true)
          .html(this.tableSVG.html())
          .attr(
            "fill",
            this.legend.find((item) => item.group_id === table.group_id)
              ?.color ?? "transparent"
          );
        targetSeat.select("g").on("click", () => this.handlerClick(table._id));
      });
    },
  },
};
</script>

<style scoped>
.map {
  height: 100%;
  width: 100%;
  padding: 24px;
  overflow: hidden;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}

.map-root {
  height: 100%;
  width: 100%;
  overflow: hidden;
  box-sizing: border-box;
}

h3 {
  margin-top: 0px;
}

::v-deep svg {
  height: 100%;
  width: 100%;
}

::v-deep .table {
  cursor: pointer;
}
</style>
