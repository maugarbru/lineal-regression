<template>
  <div>
    <canvas id="scatter-chart"></canvas>
  </div>
</template>

<script>
import Chart from 'chart.js/auto'

const scatterChartData = (regressionData, dispersionData) => ({
  type: 'scatter',
  data: {
    datasets: [
      {
        label: 'y = B0 + B1x',
        data: regressionData,
        backgroundColor: '#AA2323',
        borderColor: '#EE2323',
        borderWidth: 3,
      },
      {
        label: 'Datos',
        data: dispersionData,
        backgroundColor: '#2323AA',
        borderColor: '#2323EE',
        borderWidth: 3,
      },
    ],
  },
})

export default {
  name: 'ScatterChart',
  props: {
    regression: {
      type: Array,
      required: true,
    },
    dispersion: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      chartData: scatterChartData(this.regression, this.dispersion),
    }
  },
  mounted() {
    const ctx = document.getElementById('scatter-chart')
    // eslint-disable-next-line no-new
    new Chart(ctx, this.chartData)
  },
}
</script>
