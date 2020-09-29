<template>
  <v-app>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <v-footer
    >
      <span>&copy; {{ new Date().getFullYear() }} Wesley Azevedo</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data () {
    return {
      title: 'Carbon Intensity UK'
    }
  }
}
</script>
