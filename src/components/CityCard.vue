<template>
  <div class="city-card">
    <img :src="`http://openweathermap.org/img/wn/${city.img}@2x.png`" />
    <h2>{{ city.name }}</h2>
    <p>{{ Math.round(city.tempretaure) }} &#8451;</p>
    <button v-show="btnAdd" class="btn" @click="addToFavorite">
      Add to favorite
    </button>
    <button v-show="btnDel" class="btn" @click="removeFromFavorite">Remove</button>
  </div>
</template>

<script>
export default {
  props: {
    city: {
      type: Object,
      required: true,
    },
    // btn is optional
    btnDel: {
      type: Boolean,
      required: false,
      default: true,
    },
    btnAdd: {
      type: Boolean,
      required: false,
      default: true,
    },
  },

  data() {
    return {
      cityData: { ...this.city },
    };
  },

  methods: {
    addToFavorite() {
      this.$emit('addToFavorite', this.cityData);
    },
    removeFromFavorite(){
        this.$emit('removeFromFavorite', this.cityData)
    }
  },
};
</script>

<style lang="scss" scoped>
.city-card {
  display: flex;
  flex-direction: column;
  width: 200px;
  min-height: 300px;
  font-size: 15px;
  font-family: 'Courier New', Courier, monospace;
  border: 1px solid gray;
  align-items: center;
  justify-content: space-around;
  margin: 20px 20px;

  .btn {
    margin: 0.5rem 1rem;
    padding: 10px 15px;
    background-color: lightblue;
    border: 1px solid dimgray;
    cursor: pointer;
    font-size: 1rem;
    min-width: 145px;

    &:hover {
      background-color: dimgray;
      color: lightblue;
    }
  }
  p {
    font-size: 1.3rem;
  }
}
</style>
