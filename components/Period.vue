<template>
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
              hint="YYYY/MM/DD format"
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
            @input="newData"
          ></v-date-picker>
        </v-menu>
        <v-select
          v-model="format"
          prepend-icon="mdi-calendar-clock"
          :items="items"
          label="Period Format"
          @change="newData"
        ></v-select>
      </v-list-item-content>
    </v-list-item>
  </v-card>
</template>

<script>
export default {
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
    newData() {
        this.menu = false
        this.$emit('dataChanged', this.date)
    },
    formatDate(date) {
      if (!date) return null;

      const [year, month, day] = date.split("-");
      return `${year}/${month}/${day}`;
    },
    parseDate(date) {
      if (!date) return null;

      const [month, day, year] = date.split("/");
      return `${year}-${month.padStart(2, "0")}-${day.padStart(2, "0")}`;
    },
  },
  data() {
    return {
      format: 7,
      items: [
        { text: "1 week", value: 7 },
        { text: "2 weeks", value: 14 },
        { text: "3 weeks", value: 21 },
        { text: "1 month", value: 30 },
      ],
      date: new Date('2020-08-01').toISOString().substr(0, 10),
      dateFormatted: this.formatDate(new Date().toISOString().substr(0, 10)),
      menu: false,
    };
  },
};
</script>

<style>
</style>