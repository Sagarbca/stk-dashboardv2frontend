<template>
  <section class="content">
    <mdb-container>
      <mdb-scatter-chart
        :data="scatterChartData"
        :options="scatterChartOptions"
        :width="1000"
        :height="500"
      ></mdb-scatter-chart>
    </mdb-container>
  </section>
</template>

<script>
  import { mdbScatterChart, mdbContainer } from 'mdbvue'

  import axios from 'axios'
  const url = 'http://localhost:5000/read_excel'

  export default {
    name: 'Graph',
    components: {
      mdbScatterChart,
      mdbContainer
    },
    data() {
      return {
        // Array will be automatically processed with visualization.arrayToDataTable function
        scatterChartData: {
          datasets: [
            {
              label: 'Sum - Time',
              backgroundColor: 'rgba(73,156,239,0.77)',
              borderColor: 'rgb(44,85,194)',
              borderWidth: 0.10,
              data: [

              ]
            }
          ]
        },
        scatterChartOptions: {
          responsive: false,
          maintainAspectRatio: false,
          scales: {
            xAxes: [
              {
                gridLines: {
                  display: true,
                  color: 'rgba(0, 0, 0, 0.1)'
                }
              }
            ],
            yAxes: [
              {
                gridLines: {
                  display: true,
                  color: 'rgba(0, 0, 0, 0.1)'
                }
              }
            ]
          }
        }
      }
    },
    mounted() {
      this.fetchData()
      /* window.setInterval(() => {
        this.fetchData()
      }, 5000) */
    },
    methods: {
      fetchData() {
        axios.get(url).then(response => {
          this.makeGraphValue(response.data)
        })
      },

      makeGraphValue(value) {
        // const self = this
        console.log('this is scatterChartData chart', this.scatterChartData.datasets)
        let requiredValue = []
        value.map(item => {
          /* requiredValue.push(Object.create(null, {
            x: {value: Number(item.time)}, y: {value: Number(item.sum)}
          })) */
          this.scatterChartData.datasets.map(dataValue => {
            dataValue.data.push(Object.create(null, {
              x: {value: Number(item.time)}, y: {value: Number(item.sum)}
            }))
            console.log('this is dataValue chart', dataValue.data)
          })
        })
        console.log('logs', requiredValue)
        console.log('this is scatterChartData chart', this.scatterChartData.datasets)
      }
    }
  }
</script>

<style scoped>

</style>
