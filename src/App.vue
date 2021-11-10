<template>
  <div class="wrapper">
    <h1>Weather app</h1>
    <form class="input_area" @submit.prevent>
      <input v-model="searchCity" type="text" placeholder="City name?" />
      <button class="btn" @click="cityFetch">Search</button>
    </form>
    <city-card
      v-if="citySingle.name"
      :city="citySingle"
      :btnDel="false"
      @addToFavorite="addToFavorite"
    />
    <div class="warning" v-if="isFetchError">
      Something goes wrong, try it again
    </div>
    <div class="current" v-if="currentPositionCity.name">
      <p>Your current location</p>
      <city-card :city="currentPositionCity" :btnAdd="false" :btnDel="false" />
    </div>
    <div v-else class="current">No access for your location :(</div>
    <favorites-cities
      :cityArray="cityCards"
      @removeFromFavorite="removeFromFavorite"
    />
  </div>
</template>

<script>
import axios from 'axios';
import CityCard from './components/CityCard.vue';
import FavoritesCities from './components/FavoritesCities.vue';
const key = 'f1b7c174615009df92ae772bb54ab236';

export default {
  components: {
    CityCard,
    FavoritesCities,
  },
  data() {
    return {
      searchCity: '',
      citySingle: {},
      cityCards: [],
      isFetchError: false,
      currentPositionCords: {
        latitude: '',
        longitude: '',
      },
      currentPositionCity: {},
    };
  },
  methods: {
    async cityFetch() {
      this.citySingle = {};
      try {
        const cityWeather = await axios.get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.searchCity}&units=metric&appid=${key}`
        );
        this.isFetchError = false;

        this.citySingle.name = cityWeather.data.name;
        this.citySingle.tempretaure = cityWeather.data.main.temp;
        this.citySingle.img = cityWeather.data.weather[0].icon;
      } catch (e) {
        this.isFetchError = true;
        console.log(e);
      }
    },
    addToFavorite(data) {
      const includes = this.cityCards.filter((city) => city.name == data.name);
      // base check on the same city, better to do modal with error as props, and show any kind of error -like city already included.
      if (includes.length === 0) {
        this.cityCards.push(data);
        this.searchCity = '';
        this.citySingle = {};
        this.addToLocalStorage();
      }
      this.searchCity = '';
      this.citySingle = {};
    },
    removeFromFavorite(city) {
      const find = this.cityCards.findIndex((item) => item.name === city.name);
      this.cityCards.splice(find, 1);
    },
    addToLocalStorage() {
      localStorage.setItem('citiList', JSON.stringify(this.cityCards));
    },
  },
  created() {
    const cityListLS = localStorage.getItem('citiList')
    if(cityListLS){
      this.cityCards = JSON.parse(cityListLS);
    }
  },
  mounted() {
    navigator.geolocation.getCurrentPosition((postion) => {
      this.currentPositionCords.latitude = postion.coords.latitude;
      this.currentPositionCords.longitude = postion.coords.longitude;
    });
  },
  watch: {
    currentPositionCords: {
      async handler(cords) {
        // better to do common fetch helper, get data according params. but limited time ^_^
        const cityWeather = await axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${cords.latitude}&lon=${cords.longitude}&appid=${key}&units=metric`
        );
        this.currentPositionCity.name = cityWeather.data.name;
        this.currentPositionCity.tempretaure = cityWeather.data.main.temp;
        this.currentPositionCity.img = cityWeather.data.weather[0].icon;
      },
      deep: true,
    },
  },
};
</script>

<style lang="scss" scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.current {
  position: fixed;
  display: flex;
  flex-direction: column;
  align-items: center;
  left: 5vh;
  top: 5vh;
}

.wrapper {
  padding-top: 5vh;
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;

  .warning {
    color: red;
    font-size: 1.5rem;
  }

  input {
    margin: 1rem 1rem;
    padding: 10px 15px;
    font-size: 1.3rem;
  }

  .btn {
    margin: 1rem 1rem;
    padding: 10px 15px;
    background-color: lightblue;
    border: 1px solid dimgray;
    cursor: pointer;
    font-size: 1.3rem;

    &:hover {
      background-color: dimgray;
      color: lightblue;
    }
  }
}
</style>
