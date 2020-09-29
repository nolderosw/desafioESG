<template>
  <v-container>
    <v-row justify="center" align="center">
      <h1>Carbon Intensity UK</h1>
    </v-row>
    <v-row class="mt-10">
      <v-col cols="12" sm="12" xs="12" md="4" lg="4" xl="4">
        <period ref="period" @dataChanged="loadData" />
        <metrics :metrics="metric" />
      </v-col>
      <v-col cols="12" sm="12" xs="12" md="8" lg="8" xl="8">
        <v-card outlined>
          <v-list-item>
            <v-list-item-content>
              <bar-chart
                ref="chart"
                :key="keyChart"
                :data="barChartData"
                :options="barChartOptions"
                :height="200"
              />
            </v-list-item-content>
          </v-list-item>
        </v-card>
      </v-col>
    </v-row>
    <v-dialog v-model="dialog" hide-overlay persistent width="300">
      <v-card color="primary" dark>
        <v-card-text>
          Loading Data...
          <v-progress-linear
            indeterminate
            color="white"
            class="mb-0"
          ></v-progress-linear>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import Metrics from "@/components/Metrics.vue";
import Period from "@/components/Period.vue";
import { create, all } from "mathjs";
export default {
  components: {
    Metrics,
    Period,
  },
  async mounted() {
    await this.loadData();
  },
  methods: {
    getDates(startDate, stopDate) {
      let dateArray = new Array();
      let currentDate = startDate;
      while (currentDate <= stopDate) {
        dateArray.push(new Date(currentDate).toISOString());
        currentDate.setDate(currentDate.getDate() + 1);
      }
      return dateArray;
    },
    async loadData() {
      this.dialog = true;
      const math = create(all);
      const date = new Date(this.$refs.period.date);
      const stopDate = new Date(this.$refs.period.date);
      const dateString = date.toISOString();
      const period = Number(this.$refs.period.format);
      const dateRange = this.getDates(
        date,
        stopDate.setDate(stopDate.getDate() + period - 1)
      );
      var labels = [];
      var dayValue = [];
      var max = [];
      var average = [];
      for (let el of dateRange) {
        const response = await this.$axios.get(
          `https://api.carbonintensity.org.uk/intensity/${el}/fw24h`
        );
        labels.push(el.substr(0, 10));
        dayValue.push(response.data.data[0].intensity.actual || 0);
        max.push(
          Math.max.apply(
            Math,
            response.data.data.map(function (o) {
              return o.intensity.actual;
            })
          )
        );
        const sum = response.data.data.reduce(
          (total, next) => total + next.intensity.actual,
          0
        );
        average.push(Number(sum / response.data.data.length).toFixed(2));
      }
      this.barChartData.labels = labels;
      this.barChartData.datasets[0].data = dayValue;
      this.barChartData.datasets[1].data = max;
      this.barChartData.datasets[2].data = average;
      this.keyChart += 1;
      this.metric.average = math.mean(dayValue).toFixed(2);
      this.metric.deviation = math.std(dayValue).toFixed(2);
      this.metric.median = math.median(dayValue).toFixed(2);
      this.dialog = false;
    },
  },
  data() {
    return {
      dialog: false,
      metric: {
        average: 0,
        deviation: 0,
        median: 0,
      },
      keyChart: 0,
      barChartData: {
        labels: [],
        datasets: [
          {
            label: "CO2 (12:00 PM)",
            data: [],
            backgroundColor: "#00994c",
          },
          {
            label: "Max Day Value",
            data: [],
            backgroundColor: "#CC0000",
          },
          {
            label: "Average of Day",
            data: [],
            backgroundColor: "#000099",
          },
        ],
      },
      barChartOptions: {
        responsive: true,
        legend: {
          display: true,
        },
        tooltips: {
          backgroundColor: "#17BF62",
        },
        scales: {
          xAxes: [
            {
              gridLines: {
                display: false,
              },
            },
          ],
          yAxes: [
            {
              ticks: {
                beginAtZero: true,
              },
              gridLines: {
                display: false,
              },
            },
          ],
        },
      },
    };
  },
};
</script>
