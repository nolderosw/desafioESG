<template>
  <v-card outlined align="center" justify="center">
    <v-list-item>
      <v-list-item-content>
        <h3>Metrics</h3>
        <br />
        <span>Average: {{ metrics.average }}</span>
        <br />
        <span>Standard Deviation: {{metrics.deviation}}</span>
        <br />
        <span>Median: {{metrics.median}}</span>
      </v-list-item-content>
    </v-list-item>
  </v-card>
</template>

<script>
export default {
  props: {
    metrics: {
      type: Object,
      default: { average: 0, deviation: 0, median: 0 },
      required: true,
    },
  },
};
</script>

<style>
</style>