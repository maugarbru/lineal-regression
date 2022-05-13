<template>
  <div>
    <v-dialog v-model="loading" hide-overlay persistent width="300">
      <v-card color="primary" dark>
        <v-card-text>
          Cargando datos
          <v-progress-linear
            indeterminate
            color="white"
            class="mb-0"
          ></v-progress-linear>
        </v-card-text>
      </v-card>
    </v-dialog>
    <v-stepper v-model="e1">
      <v-stepper-header>
        <v-stepper-step :complete="e1 > 1" step="1">
          Columnas para analizar
        </v-stepper-step>
        <v-divider></v-divider>
        <v-stepper-step :complete="e1 > 2" step="2">Función</v-stepper-step>
        <v-divider></v-divider>
        <v-stepper-step step="3">Gráfica</v-stepper-step>
      </v-stepper-header>

      <v-stepper-items>
        <v-stepper-content step="1">
          <v-card>
            <v-card-text>
              <v-card height="200px" class="overflow-auto px-2 py-2">
                <v-simple-table>
                  <template #default>
                    <thead>
                      <tr>
                        <th :id="column1">{{ column1 }}</th>
                        <th :id="column2">{{ column2 }}</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(item, i) in data" :key="i">
                        <td>{{ item[column1] }}</td>
                        <td>{{ item[column2] }}</td>
                      </tr>
                    </tbody>
                  </template>
                </v-simple-table>
              </v-card>
            </v-card-text>
            <v-card-actions>
              <v-spacer />
              <v-btn color="primary" @click="e1 = 2"
                >Siguiente</v-btn
              ></v-card-actions
            >
          </v-card>
        </v-stepper-content>

        <v-stepper-content step="2">
          <v-card>
            <v-card-text>
              <div>
                <h2><code>y = B0 + B1 * X</code></h2>
                <h2>
                  <code>y = {{ beta0 }} + {{ beta1 }} * X</code>
                </h2>
              </div>

              <v-sparkline
                v-if="Yvalues"
                :value="Yvalues"
                color="red"
                line-width="2"
                padding="16"
              ></v-sparkline>
              <div>
                <h2>
                  Pendiente: <code>{{ beta1 }}</code>
                </h2>
                <h4 v-if="beta1 > 0">
                  Como la pendiente es
                  <span class="secondary--text font-weight-bold">POSITIVA</span
                  >. Tienen una relación lineal directa.
                </h4>
                <h4 v-else-if="beta1 < 0">
                  Como la pendiente es
                  <span class="orange--text font-weight-bold">NEGATIVA</span>.
                  Tienen una relación lineal inversa.
                </h4>
                <h4 v-else>
                  Como la pendiente es
                  <span class="black--text font-weight-bold">CERO</span>. No
                  existe relación lineal.
                </h4>
              </div>
              <h2>
                Intersecto: <code>{{ beta0 }}</code>
              </h2>
            </v-card-text>
            <v-card-actions
              ><v-btn text @click="e1 = 1">Atras</v-btn>
              <v-spacer />
              <v-btn color="primary" @click="e1 = 3">Siguiente</v-btn>
            </v-card-actions>
          </v-card>
        </v-stepper-content>

        <v-stepper-content step="3">
          <v-card>
            <v-card-text
              ><Scatter
                v-if="result && original"
                :regression="result"
                :dispersion="original"
            /></v-card-text>
            <v-card-actions
              ><v-btn text @click="e1 = 2">Atras</v-btn>
            </v-card-actions>
          </v-card>
        </v-stepper-content>
      </v-stepper-items>
    </v-stepper>
  </div>
</template>

<script>
import Scatter from './Scatter.vue'
export default {
  name: 'ResultComponent',
  components: { Scatter },
  props: {
    data: {
      type: Array,
      required: true,
    },
    column1: {
      type: String,
      required: true,
    },
    column2: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      labels: ['12am', '3am', '6am', '9am', '12pm', '3pm', '6pm', '9pm'],
      value: [200, 675, 410, 390, 310, 460, 250, 240],
      loading: true,
      dialog: false,
      e1: 1,
      result: null,
      original: null,
      beta0: null,
      beta1: null,
      Yvalues: null,
    }
  },
  watch: {},
  mounted() {
    this.loading = true
    this.findLineByLeastSquares(this.data, this.column1, this.column2)
    this.loading = false
  },
  methods: {
    /**
     * Función para calcular la línea de regresión por mínimos cuadrados
     */
    findLineByLeastSquares(data, columnX, columnY) {
      let sumXY = 0
      let sumXX = 0
      let sumY = 0
      let sumX = 0
      const dispersion = []
      for (const item of data) {
        const Xi = parseFloat(item[columnX])
        const Yi = parseFloat(item[columnY])
        dispersion.push({ x: Xi, y: Yi })
        sumXY += Xi * Yi
        sumXX += Xi * Xi
        sumY += Yi
        sumX += Xi
      }

      const beta1 =
        (data.length * sumXY - sumY * sumX) /
        (data.length * sumXX - sumX * sumX)
      const beta0 = sumY / data.length - beta1 * (sumX / data.length)

      const results = []
      for (const item of data) {
        const x = parseFloat(item[columnX])
        const y = beta1 * x + beta0
        results.push({ x, y })
      }

      this.beta1 = beta1
      this.beta0 = beta0
      this.result = results
      this.original = dispersion
      this.Yvalues = [results[0].y, results[results.length - 1].y]
    },
  },
}
</script>
