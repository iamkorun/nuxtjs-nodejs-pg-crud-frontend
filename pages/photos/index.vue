<template>
  <div class="container">
    <h1>Photos</h1>
    <!-- <li v-for="item in items">
      {{ item }}
    </li> -->
    <div class="photos">
      <nuxt-link
        v-for="photo in photos"
        :to="{ path: `/photos/${photo.id}` }"
        :key="photo.id"
        ><img :src="photo.download_url" class="photo-item" />
        {{ photo.author }}</nuxt-link
      >
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: [{ message: "Foo" }, { message: "Bar" }],
      photos: [],
    };
  },
  async created() {
    const photos = await this.$axios.$get(
      "https://picsum.photos/v2/list?limit=12"
    );
    this.photos = photos;
    console.log(this.items);
    console.log({ photos });
  },
};
</script>

<style scoped>
.container {
  padding: 1em;
  width: 1220px;
  margin: 0 auto;
}
.photos {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: auto;
  grid-gap: 1em;
  text-align: center;
}

.photo-item {
  width: 100%;
  height: 256px;
  object-fit: cover;
}
</style>
