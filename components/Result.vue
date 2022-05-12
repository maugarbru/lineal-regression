<template>
  <div></div>
</template>

<script>
export default {
  name: 'ResultComponent',
  props: {
    data: {
      type: Array,
      required: true
    },
    column1: {
      type: String,
      required: true
    },
    column2: {
      type: String,
      required: true
    },
  },
  data() {
    return {
      dialog: false,
    }
  },
  watch: {},
  methods: {
    /**
     * Función para calcular la línea de regresión por mínimos cuadrados
     * @param {Array<number>} valuesX
     * @param {Array<number>} valuesY
     */
    findLineByLeastSquares(valuesX, valuesY) {
      let sumX = 0
      let sumY = 0
      let sumXY = 0
      let sumXX = 0
      let count = 0

      let x = 0
      let y = 0
      const valuesLength = valuesX.length

      if (valuesLength !== valuesY.length) {
        throw new Error(
          'The parameters valuesX and valuesY need to have same size!'
        )
      }
      if (valuesLength === 0) {
        return [[], []]
      }

      for (let v = 0; v < valuesLength; v++) {
        x = valuesX[v]
        y = valuesY[v]
        sumX += x
        sumY += y
        sumXX += x * x
        sumXY += x * y
        count++
      }

      /*
       * Calcular m y b para la fórmula:
       * y = x * m + b
       */
      const m = (count * sumXY - sumX * sumX) / (count * sumXX - sumX * sumX)
      const b = sumY / count - (m * sumX) / count

      const resultValuesX = []
      const resultValuesY = []

      for (let v = 0; v < valuesLength; v++) {
        x = valuesX[v]
        y = x * m + b
        resultValuesX.push(x)
        resultValuesY.push(y)
      }

      return [resultValuesX, resultValuesY]
    },
  },
}
</script>
