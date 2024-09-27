<template>
  <CountriesListing :countries="countries"/>
  <AddNewCountry @countryAdded="fetchCountries"/>
</template>

<script>
import CountriesListing from './components/CountriesListing.vue';
import AddNewCountry from './components/AddNewCountry.vue';
export default {
  name: 'App',
  components: {
    CountriesListing,
    AddNewCountry
  },
  data() {
    return {
      countries: []
    };
  },
  methods: {
    async fetchCountries() {
      const response = await fetch("http://localhost:8080/countries");
      this.countries = await response.json();
    }
  },
  created() {
    this.fetchCountries();
  }
}
</script>

<style>

</style>
