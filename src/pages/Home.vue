<template>
  <div>
    <button class="light-dark-btn" @click="toggleAppearence()">
      Click me!
    </button>

    <h3 :class="appearenceMode">Hows the weather looking?</h3>
    <input id="searchTextField" type="text" size="50" />
    <div>
      <button class="transition-btn" @click="back()">Back</button>
      <button class="transition-btn" @click="next()">Next</button>
    </div>

    <transition-group
      v-for="index in daysTotal"
      :key="index"
      tag="div"
      :name="isBack ? 'slideback' : 'slide'"
    >
      <WeatherCard
        v-if="currentIndex === index"
        :date-timestamp="weatherData.daily[currentIndex - 1].dt"
        :weather-description="
          weatherData.daily[currentIndex - 1].weather[0].description
        "
        :temperature="weatherData.daily[currentIndex - 1].temp.day"
        :location="placeName"
        :icon-code="weatherData.daily[currentIndex - 1].weather[0].icon"
      />
    </transition-group>
  </div>
</template>

<script>
import WeatherCard from '../components/WeatherCard.vue';
import config from '../config';

export default {
  name: 'App',
  components: {
    WeatherCard,
  },
  data() {
    return {
      isBack: false,
      currentIndex: 1,
      weatherData: [],
      daysTotal: 0,
      placeName: 'Sydney',
      lat: -33.8688197, //default to sydney
      long: 151.2092955, //default to sydney
      appearenceMode: 'light',
    };
  },
  mounted() {
    document.body.className = this.appearenceMode;
    const script = document.createElement('script');
    script.async = true;
    script.src = `https://maps.googleapis.com/maps/api/js?key=${config.GOOGLE_API_KEY}&libraries=places`;
    document.head.appendChild(script);
    script.onload = () => {
      this.setupAutocomplete();
    };

    this.fetchData(this.lat, this.long, this.placeName);
  },
  methods: {
    async fetchData(lat, long, placeName) {
      const response = await fetch(
        `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${long}&appid=b0c12f55341bce9611595adea62d480c&exclude=hourly,minutely,alerts&units=metric`
      );

      this.weatherData = await response.json();
      this.daysTotal = this.weatherData.daily.length;
      this.placeName = placeName;
    },
    setupAutocomplete() {
      const input = document.getElementById('searchTextField');
      const options = {
        componentRestrictions: {
          country: 'au',
        },
      };
      // eslint-disable-next-line no-undef
      var autocomplete = new google.maps.places.Autocomplete(input, options);
      // eslint-disable-next-line no-undef
      google.maps.event.addListener(autocomplete, 'place_changed', () => {
        const place = autocomplete.getPlace();
        const lat = place.geometry.location.lat();
        const long = place.geometry.location.lng();
        this.fetchData(lat, long, place.name);
      });
    },
    toggleAppearence() {
      console.log(this.appearenceMode);
      this.appearenceMode = this.appearenceMode === 'light' ? 'dark' : 'light';
      document.body.className = this.appearenceMode;
    },
    back() {
      this.isBack = true;
      if (this.currentIndex > 1) {
        this.currentIndex--;
      }
    },
    next() {
      this.isBack = false;
      if (this.currentIndex < this.weatherData.daily.length) {
        this.currentIndex++;
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}

@import '../assets/css/home.css';
</style>
