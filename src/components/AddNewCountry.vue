<template>
  <form @submit.prevent="addCountry">
    <div>
      <label for="name">Country Name:</label>
      <input
        id="name"
        v-model="newCountry.name"
        :class="{'error-input': nameError}"
        placeholder="Country Name"
        required
        minlength="3"
        maxlength="20"
      />
      <span v-if="nameError" class="error">{{ nameError }}</span>
    </div>

    <div>
      <label for="continent">Continent:</label>
      <select
        id="continent"
        v-model="newCountry.continent"
        required
      >
        <option value="" disabled>Select Continent</option>
        <option v-for="continent in uniqueContinents" :key="continent" :value="continent">
          {{ continent }}
        </option>
      </select>
    </div>

    <div>
      <label for="rank">Rank:</label>
      <input
        id="rank"
        type="number"
        :class="{'error-input': rankError}"
        v-model.number="newCountry.rank"
        placeholder="Rank"
        required
        min="1"
      />
      <span v-if="rankError" class="error">{{ rankError }}</span>
    </div>

    <div>
      <label for="image">Country Image:</label>
      <input
        id="image"
        type="file"
        @change="handleFileUpload"
        accept=".jpg,.jpeg,.png"
        required
      />
      <span v-if="imageError" class="error">{{ imageError }}</span>
    </div>

    <button type="submit" :disabled="loading || !isFormValid">Add Country</button>
  </form>
</template>

<script>
export default {
  data() {
    return {
      newCountry: {
        name: '',
        continent: '',
        rank: null,
        image: null,
      },
      uniqueContinents: [],
      nameError: '',
      rankError: '',
      imageError: '',
      loading: false,
      isFormValid: true, 
    };
  },
  methods: {
    async fetchUniqueContinents() {
      const response = await fetch('http://localhost:8080/countries');
      const countries = await response.json();
      const continentsSet = new Set(countries.map(country => country.continent));
      this.uniqueContinents = Array.from(continentsSet);
    },
    handleFileUpload(event) {
      const file = event.target.files[0];
      if (file) {
        const validTypes = ['image/jpeg', 'image/png'];
        if (validTypes.includes(file.type) && file.size <= 4 * 1024 * 1024) {
          this.newCountry.image = file;
          this.imageError = '';
        } else {
          this.imageError = 'Only JPG and PNG files are allowed and must be less than 4 MB.';
        }
      }
    },
    validateInputs() {
      this.nameError = '';
      this.rankError = '';

      if (this.newCountry.name.length < 3 || this.newCountry.name.length > 20) {
        this.nameError = 'Name must be between 3 and 20 characters.';
      }

      if (this.newCountry.rank <= 0 || isNaN(this.newCountry.rank)) {
        this.rankError = 'Rank must be a positive number.';
      }

      this.isFormValid = !this.nameError && !this.rankError && !this.imageError;
      return this.isFormValid;
    },
    async addCountry() {
      if (!this.validateInputs() || this.loading) return; 

      this.loading = true;

      const response = await fetch('http://localhost:8080/countries');
      const countries = await response.json();
      console.log(countries, "countries", this.newCountry)
      const exists = countries.some(country => country.name.toLowerCase() === this.newCountry.name.toLowerCase());
      const existsRank = countries.some(country => country.rank === this.newCountry.rank);
        console.log(existsRank, "existsRank")
        this.nameError = '';
        this.rankError = '';

        if (exists) {
        this.nameError = 'Country already exists';
        }

        if (existsRank) {
        this.rankError = 'Rank already exists';
        }

        if (this.nameError || this.rankError) {
        this.loading = false; 
        return; 
        }

      const formData = new FormData();
      formData.append('name', this.newCountry.name);
      formData.append('continent', this.newCountry.continent);
      formData.append('rank', this.newCountry.rank);
      formData.append('image', this.newCountry.image);
      console.log(formData, "formdata");

      const addResponse = await fetch('http://localhost:8080/country', {
        method: 'POST',
        body: formData,
      });

      this.loading = false; 

      if (addResponse.ok) {
        alert("Added")
        this.$emit('countryAdded');
        this.resetForm();
      } else {
        console.error('Failed to add country.');
      }
    },
    resetForm() {
      this.newCountry.name = '';
      this.newCountry.continent = '';
      this.newCountry.rank = null;
      this.newCountry.image = null;
    },
  },
  created() {
    this.fetchUniqueContinents();
  },
};
</script>

<style scoped>
.error {
  color: red;
  font-size: 0.9em;
}
.error {
  color: red;
  font-size: 0.9em;
}
.error-input {
  border: 1px solid red; 
}

</style>
