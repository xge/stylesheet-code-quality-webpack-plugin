<template>
  <div class="v-box w-2-3 files">
    <div class="v-box__header">
      SCSS Dateien
      <div class="tooltip-icon" 
        v-tooltip='{content: help, placement: "right", targetClasses: ["cq-tooltip"],}' 
      >
        <font-awesome-icon icon="question-circle" />
      </div>
    </div>
    <div class="v-box__chart  v-box__content filesChart">
      <highcharts :options="chartOptions" :updateArgs="updateArgs" ></highcharts>
    </div>
  </div>
</template>


<script>
import Vue from 'vue';
import {Chart} from 'highcharts-vue'

import VTooltip from 'v-tooltip'
Vue.use(VTooltip)

import { library } from '@fortawesome/fontawesome-svg-core'
import { faQuestionCircle} from '@fortawesome/free-solid-svg-icons'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'

library.add(faQuestionCircle)


export default Vue.extend({
  name: 'SpecificityChart',
  components: {
    highcharts: Chart,
    FontAwesomeIcon,    
  },
  props: {
    dataseries: {
      type: Array,
      required: true
    },
    categories: {
      type: Array,
      required: true
    },
  },
  watch: {
    dataseries(data) {
      this.dataseries = data
      this.chartOptions.series = this.dataseries      
    },
    categories(data) {
      this.categories = data  
      this.chartOptions.xAxis.categories = this.categories
    },
  },
  computed: {
    clientWidth() {
      return document.querySelector('.files').clientWidth
    },
  },
  data() {
    return {
      help: `
      Die Visualisierung zeigt den Anteil an Selektoren, Deklarationen und gefundenen Warnungen pro SCSS Datei. 
      Die einfache Übersicht gibt Aufschluss über fehlerbehaftete Dateien.
      `,
      updateArgs: [true, true, {duration: 500}],
      chartOptions: {
        series: this.dataseries,
        chart: {
          type: 'column',
          height: 500,
        },
        title: {
          text: null
        },
        xAxis: {
          title: {
            text: 'SCSS Dateien'
          },
          categories: this.categories,
          crosshair: true,
          labels: {
            formatter: function() {
              const sV = this.value.split('/')
              const tspan = sV[sV.length-1]
              return '<tspan>' + tspan + '</tspan>';
            }
          }
        },
        tooltip: {
          headerFormat: '<span style="font-size:10px">{point.key}</span><table>',
          pointFormat: '<tr><td style="color:{series.color};padding:0">{series.name}: </td>' +
            '<td style="padding:0"><b>{point.y}</b></td></tr>',
          footerFormat: '</table>',
          shared: true,
          useHTML: true
        },
        yAxis: {
          min: 0,
          title: {
            text: 'Anzahl'
          }
        },
        plotOptions: {
          column: {
            pointPadding: 0.2,
            borderWidth: 0
          }
        },
      }
    }
  },
  methods: {
    
  },
  mounted() {
    this.chartOptions.chart.width = this.clientWidth
  }
});
</script>

<style scoped>
.files {
  grid-column: 1 / 3;
  grid-row: 2 / 3;
}

</style>
