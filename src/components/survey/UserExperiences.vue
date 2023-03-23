<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperience"
          >Load Submitted Experiences</base-button
        >
      </div>
      <p v-if="isLoading">Loading</p>
      <p v-else-if="!isLoading && error">{{ error }}</p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No stored experiences found start adding
      </p>
      <ul v-else>
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        > <span class="material-icons" @click="deleteExperience(result.id)">
          delete
        </span></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';
import axios from 'axios';

export default {
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      error: null,
    };
  },
  methods: {
    async loadExperience() {
      this.isLoading = true;
      try {
        const response = await axios.get(
          'https://vue-demo-6e122-default-rtdb.firebaseio.com/surveys.json'
        );
        if (response.status !== 200) {
          throw new Error('Network response was not ok');
        }
        const data = response.data;
        console.log(data);
        const results = [];
        for (const id in data) {
          results.push({
            id: id,
            name: data[id].name,
            rating: data[id].rating,
          });
        }
        this.results = results;
        this.error = null;
      } catch (error) {
        console.log(error);
        this.error = 'Error retrieving data 🥺 Please try again';
      } finally {
        this.isLoading = false;
      }
    },
  },
  async deleteExperience(id) {
      this.isLoading = true;
      try {
        const response = await axios.delete(`https://vue-demo-6e122-default-rtdb.firebaseio.com/surveys/${id}.json`);
        if (response.status !== 200) {
          throw new Error('Network response was not ok');
        }
        this.results = this.results.filter(result => result.id !== id);
        this.error = null;
      } catch (error) {
        console.log(error);
        this.error = 'Error deleting data 🥺 Please try again';
      } finally {
        this.isLoading = false;
      }
    },
  
  mounted() {
    this.loadExperience();
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
