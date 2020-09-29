<template>
  <v-container>
    <v-row justify="center" align="center">
      <h1>Carbon Intensity UK</h1>
    </v-row>
    <v-row class="mt-10">
      <v-col cols="12" sm="12" xs="12" md="4" lg="4" xl="4">
        <v-card class="mb-4" outlined align="center" justify="center">
          <v-list-item>
            <v-list-item-content>
              <h3>Period</h3>
              <v-menu
                v-model="menu"
                :close-on-content-click="false"
                transition="scale-transition"
                offset-y
                max-width="290px"
                min-width="290px"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-text-field
                    v-model="computedDateFormatted"
                    label="Start Period"
                    hint="MM/DD/YYYY format"
                    persistent-hint
                    prepend-icon="mdi-calendar"
                    readonly
                    v-bind="attrs"
                    v-on="on"
                  ></v-text-field>
                </template>
                <v-date-picker
                  v-model="date"
                  no-title
                  @input="menu = false"
                ></v-date-picker>
              </v-menu>
              <v-select
                prepend-icon="mdi-calendar-clock"
                :items="items"
                label="Period Format"
              ></v-select>
            </v-list-item-content>
          </v-list-item>
        </v-card>
        <v-card outlined align="center" justify="center">
          <v-list-item>
            <v-list-item-content>
              <h3>Metrics</h3>
              <br>
              <span>Average: </span>
              <br>
              <span>Standard Deviation: </span>
              <br>
              <span>Median: </span>
            </v-list-item-content>
          </v-list-item>
        </v-card>
      </v-col>
      <v-col cols="12" sm="12" xs="12" md="8" lg="8" xl="8">
        <v-card outlined>
          <v-list-item>
            <v-list-item-content>
              <bar-chart
                :data="barChartData"
                :options="barChartOptions"
                :height="200"
              />
            </v-list-item-content>
          </v-list-item>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  async mounted() {
    const teste = await this.$axios.get(
      "https://viacep.com.br/ws/01001000/json/"
    );
    console.log(teste.data);
  },
  computed: {
    computedDateFormatted() {
      return this.formatDate(this.date);
    },
  },
  watch: {
    date(val) {
      this.dateFormatted = this.formatDate(this.date);
    },
  },
  methods: {
    formatDate(date) {
      if (!date) return null;

      const [year, month, day] = date.split("-");
      return `${month}/${day}/${year}`;
    },
    parseDate(date) {
      if (!date) return null;

      const [month, day, year] = date.split("/");
      return `${year}-${month.padStart(2, "0")}-${day.padStart(2, "0")}`;
    },
  },
  data() {
    return {
      items: ["Foo", "Bar", "Fizz", "Buzz"],
      date: new Date().toISOString().substr(0, 10),
      dateFormatted: this.formatDate(new Date().toISOString().substr(0, 10)),
      menu: false,
      picker: new Date().toISOString().substr(0, 10),
      barChartData: {
        labels: [
          "2019-06",
          "2019-07",
          "2019-08",
          "2019-09",
          "2019-10",
          "2019-11",
          "2019-12",
          "2020-01",
          "2020-02",
          "2020-03",
          "2020-04",
          "2020-05",
        ],
        datasets: [
          {
            label: "Visits",
            data: [10, 15, 20, 30, 40, 50, 60, 70, 34, 45, 11, 78, 45],
            backgroundColor: "#003f5c",
          },
          {
            label: "Pages Views",
            data: [30, 24, 57, 23, 68, 72, 25, 64, 133, 143, 165, 33, 56],
            backgroundColor: "#2f4b7c",
          },
          {
            label: "Users",
            data: [45, 65, 30, 53, 34, 35, 26, 37, 34, 45, 67, 87, 98],
            backgroundColor: "#665191",
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
