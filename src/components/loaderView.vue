<template>
  <div class="text-center">
      <v-overlay v-model="overlay">
        <v-progress-circular
          :width="3"
          color="red"
          indeterminate
      ></v-progress-circular>
      </v-overlay>
  </div>
</template>

<script>
export default {
  data: () => ({
      overlay: true,
    }),
}
</script>

<style>

</style>