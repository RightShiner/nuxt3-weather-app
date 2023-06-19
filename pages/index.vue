<template>
  <p>Welcome to WeatherWiz. Search for the city you want!</p>
  <div class="content-wrapper">
    <img src="../assets/globe.png" alt="A picture of Earth" class="index-img" />
  </div>
</template>

<script>
</script>

<style scoped>
.content-wrapper {
  margin: 0 auto;
  max-width: 1200px;
  display: flex;
  justify-content: center;
}

p {
  text-align: center;
  margin-top: 15vh;
  font-size: 2rem;
}

.index-img {
  width: 400px;
  background: transparent;
  margin-top: 40px;
}
</style>