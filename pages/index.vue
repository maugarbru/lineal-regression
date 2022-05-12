<template>
  <v-row justify="center" align="center">
    <v-col cols="12" md="8">
      <v-card>
        <v-card-title class="headline font-weight-bold">Subir archivo</v-card-title>
        <v-card-text>
          <v-file-input
            v-model="file"
            accept=".csv"
            label="Archivo .csv"
            color="red"
            show-size
            prepend-inner-icon="mdi-file"
            prepend-icon=""
            truncate-length="20"
            outlined
          ></v-file-input>
          <div v-if="fileUploaded">
            <p class="green--text">Archivo subido!</p>
            <v-expansion-panels>
              <v-expansion-panel>
                <v-expansion-panel-header>Ver archivo</v-expansion-panel-header>
                <v-expansion-panel-content>
                  <v-card height="200" class="overflow-auto px-2 py-2">
                    <code>{{ fileCSVString }}</code>
                  </v-card>
                </v-expansion-panel-content>
              </v-expansion-panel>
            </v-expansion-panels>
          </div>
        </v-card-text>
      </v-card>
      <br />
      <v-card v-if="fileUploaded">
        <v-card-title class="headline font-weight-bold">Seleccionar separador</v-card-title>
        <v-card-text>
          <v-select
            v-model="delimeter"
            :items="delimeterOptions"
            item-text="label"
            item-value="value"
            color="red"
            label="Separador de las columnas en el CSV"
            outlined
          ></v-select>
        </v-card-text>
      </v-card>
      <br />
      <v-card v-if="delimeter && fileCSV">
        <v-card-title class="headline font-weight-bold"
          >Seleccionar columnas del CSV</v-card-title
        >
        <v-card-text>
          <v-row>
            <v-col cols="12" md="6">
              <v-select
                v-model="columnSelected1"
                :items="columnOptions"
                color="red"
                label="Columna #1"
                outlined
              ></v-select>
            </v-col>
            <v-col cols="12" md="6">
              <v-select
                v-model="columnSelected2"
                :items="columnOptions"
                color="red"
                label="Columna #2"
                outlined
              ></v-select>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="red" dark elevation="2">Calcular</v-btn>
          <v-spacer />
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      file: null,
      fileCSV: null,
      fileCSVString: '',
      fileUploaded: false,
      delimeter: null,
      delimeterOptions: [
        { label: 'Coma ( , )', value: ',' },
        { label: 'Punto y coma ( ; )', value: ';' },
      ],
      columnSelected1: null,
      columnSelected2: null,
      columnOptions: [],
    }
  },
  watch: {
    file() {
      if (this.file) {
        this.fileUploaded = false
        const reader = new FileReader()
        const context = this
        reader.onload = function (event) {
          context.fileCSVString = event.target.result
          context.fileUploaded = true
        }
        reader.readAsText(this.file)
      }
    },
    delimeter() {
      if (this.delimeter) {
        this.csvToArray(this.fileCSVString, this.delimeter)
        if (this.fileCSV) {
          this.columnOptions = Object.keys(this.fileCSV[0])
        }
      }
    },
  },
  methods: {
    /**
     * Función para convertir el .CSV a un array de Javascript
     * @param {*} str Contenido del CSV en string
     * @param {*} delimiter Separador de las columnas
     */
    csvToArray(str, delimiter = ',') {
      const headers = str.slice(0, str.indexOf('\n')).split(delimiter)
      const rows = str.slice(str.indexOf('\n') + 1).split('\n')

      this.fileCSV = rows.map(function (row) {
        const values = row.split(delimiter)
        return headers.reduce(function (object, header, index) {
          object[header] = values[index]
          return object
        }, {})
      })
    },
  },
}
</script>
