<template>
  <p>Your favorite</p>
  <div class="wrapper" v-if="cityArray.length > 0">
    <city-card
      v-for="city in cityArray"
      :key="city.name"
      :city="city"
      :btnAdd="false"
      @removeFromFavorite="removeFromFavorite"
    />
  </div>
</template>

<script>
import CityCard from './CityCard.vue';
export default {
  components: {
    CityCard,
  },
  props: {
    cityArray: {
      require: true,
      default: [],
    },
  },
  methods: {
      removeFromFavorite(city){
          this.$emit('removeFromFavorite', city)
      }
  }
};
</script>

<style lang="scss" scoped>
p {
  font-family: 'Courier New', Courier, monospace;
}
.wrapper {
  width: 60%;
  border-top: 2px solid grey;
  display: flex;
  flex-wrap: wrap;
}
</style>
