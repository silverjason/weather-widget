<template>
  <div class="card">
    <div class="day">{{ getDayString }}</div>
    <div class="date">{{ getDateString }}</div>
    <div class="weather-description">{{ weatherDescription }}</div>
    <div class="temperature">
      <span>{{ temperature }}c</span>
      <img :src="`http://openweathermap.org/img/w/${iconCode}.png`" />
    </div>
    <div class="location">{{ location }}</div>
  </div>
</template>

<script>
export default {
  name: 'Card',
  props: {
    dateTimestamp: { type: Number, required: true },
    weatherDescription: { type: String, required: true },
    temperature: { type: Number, required: true },
    location: { type: String, required: true },
    iconCode: { type: String, required: true },
  },
  computed: {
    getDayString() {
      const milliseconds = this.dateTimestamp * 1000;
      const dateObject = new Date(milliseconds);
      return dateObject.toLocaleString('en-US', { weekday: 'long' });
    },
    getDateString() {
      const milliseconds = this.dateTimestamp * 1000;
      const dateObject = new Date(milliseconds);
      return dateObject.toLocaleString('en-US', {
        day: 'numeric',
        month: 'long',
        year: 'numeric',
      });
    },
  },
};
</script>

<style scoped src="../assets/css/weather-card.css"></style>
