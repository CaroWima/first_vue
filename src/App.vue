<template>
  <div id="app">
      <h1 class="pgTitle"> {{ title }} </h1>

      <span class="pgDescription">{{ currentDate }}</span>
 
      <ul class="entry-list">
        <li v-for="entry in filteredEntries"
        :key="entry.id"
        class="entry-item"
        >
          <span class="day-time">{{ entry[0] }} Uhr, {{ entry[1].replaceAll("/", ".") }} </span>
          <h3 class="entry-title"> {{ entry[2] }} </h3>
          <span class="entry-description"> {{ entry[3] }} </span>
        </li>
      </ul>

    
    <footer class="footer">
      <img src="./assets/STZH_SEB_Logo.png" 
      alt="Stadt Zürich Soziale Betriebe und Einrichtungen Logo">
      <img src="./assets/Opportunity.png" 
      alt="Opportunity Logo">
      <img src="./assets/SAG_Logo_De.png" 
      alt="Stiftung Arbeitsgestaltung Logo">
    </footer>
  </div>
 
</template>

<script>
import axios from "axios";

export default {
  name: 'App',
  data() {
    return {
    title: "Welcome to Opportunity",
    currentDate: "",
    gsheet_url: "https://sheets.googleapis.com/v4/spreadsheets/1puMNlGSN8sZzy5DDR0j5kbWkLtWqjh1jMEh9lw7ade4/values:batchGet?ranges=A1%3AE100&valueRenderOption=FORMATTED_VALUE&key=AIzaSyBesotaNgSaTUIhrSKjEaExdi-ksKInhoE",
    entries:[],
    };
  },
  computed: { // computed properties are like data properties, but with a method combined, it gets executed automatically instead of calling a function explicitly
      filteredEntries() {
        return [...this.entries].slice(1); //remove first item of array
      },
    },
    methods: {
    getData() {
      axios.get(this.gsheet_url).then((response) => {
        this.entries = response.data.valueRanges[0].values;
        console.log(response);
      });
    },
    
    updateCurrentDate() {
      let today = new Date();
      const date = `${today.getDate()}
        .${today.getMonth() + 1}
        .${today.getFullYear()}`;
      this.currentDate = date;
    },
    refreshData() {
      this.getData();
      this.updateCurrentDate();
    },
  },
  mounted() {
    this.refreshData();  //get first initial data and then wait for the next update
    setInterval(() => {
      this.refreshData();
    }, 1800000) //wait 30min for next update
  } 
};

</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@500;900&display=swap');

body {
  background-color: #E8EFF4;
}

#app {
  font-family: Avenir, Inter;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #323d4a;
  margin: 60px;
}

.pgTitle {
  font-size: 62px;
  font-weight: 900;
  margin: 80px 0 20px 0;
}

.pgDescription {
  font-size: 62px;
  font-weight: 500;
  color: #9aa7b1;
  margin: 0;
}

.entry-list {
  padding-left: 0;
}

.entry-item {
  padding: 35px 40px;
  margin: 40px 0;
  font-size: 28px;
  line-height: 1.3;
  list-style:none;
  background: #0F05A0;
}

.day-time {
  font-weight:900;
  color: #EB5E00;
}

.entry-title {
  font-size: inherit;
  font-weight:900;
  color: #FFBFAB;
  margin: 0;
}

.entry-description{
  font-weight:500;
  color: #FFBFAB;
}

.footer {
  display: flex;
  justify-content: space-between;
  box-sizing: border-box;
  padding: 40px;
  background: #fff;
  position: fixed;
  bottom: 0;
  left: 0;
  width:100%;

}

.footer img {
  height: 50px;
}


</style>
