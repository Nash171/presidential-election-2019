<template>
  <div class="">
    <select v-model="selectedDistrict">
      <option v-for="district in districts" :key="district.id"  v-bind:value="district.id">
        {{ district.name }}
      </option>
    </select>
    <div class="">
      {{ selectedDistrict }}
    </div>
  </div>
</template>

<script>
  export default {
    name: 'Overview',
    props: {
      districts: Array,
      selectedDivision: Number
    },
  }
</script>

<style scoped>

</style>
