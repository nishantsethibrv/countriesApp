<template>
    <div>
      <h2>Select a Country</h2>
      <select v-model="selectedCountry" @change="displayCountryDetails">
        <option value="" disabled>Select a country</option>
        <option v-for="country in countries" :key="country.id" :value="country">
          {{ country.name }}
        </option>
      </select>
  
      <div v-if="selectedCountry">
        <h3>Country Details</h3>
        <img :src="`http://localhost:8080/${selectedCountry.flag}`" alt="Country Flag" class="country-flag" />
        <p><strong>Name:</strong> {{ selectedCountry.name }}</p>
        <p><strong>Rank:</strong> {{ selectedCountry.rank }}</p>
        <p><strong>Continent:</strong> {{ selectedCountry.continent }}</p>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      countries: {
        type: Array,
        required: true,
      },
    },
    data() {
      return {
        selectedCountry: null,
      };
    },
    methods: {
      displayCountryDetails() {
      },
    },
  };
  </script>
  
  <style scoped>
  .country-flag {
    width: 100px; 
    height: auto; 
    margin-right: 10px; 
  }
  </style>
  