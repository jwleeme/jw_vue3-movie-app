<template>
  <div class="about">
    <div class="photo">
      <Loader
        v-if="imageLoading"
        absolute />
      <img
        :src="image"
        :alt="name" />
    </div>
    <div class="name">
      {{ name }}
    </div>
    <div>{{ email }}</div>
    <div>{{ blog }}</div>
    <div>{{ phone }}</div>
  </div>
</template>

<script>
// Vuex helpers
import { mapState } from 'vuex'
import Loader from '~/components/Loader'

export default {
  components: {
    Loader
  },
  data() {
    return {
      imageLoading: true
    }
  },
  computed: {
    // about 모듈 안에 있는 image,name...등 상태 등록
    ...mapState('about', [
      'image',
      'name',
      'email',
      'blog',
      'phone'
    ])
  },
  mounted() {
    this.init()
  },
  methods: {
    async init() {
      await this.$loadImage(this.image)
      this.imageLoading =false
    }
  }
}
</script>

<style lang="scss" scoped>
.about {
  text-align: center;
  .photo {
    position: relative;
    width: 280px;
    height: 270px;
    margin: 40px auto 20px;
    border: 10px solid $gray-300;
    border-radius: 50%;
    box-sizing: border-box;
    background-color: $gray-200;
    img {
      width: 100%;
      height: 250px;
      border-radius: 50%; 
      background-size: cover;
    }
  }
  .name {
    font-size: 40px;
    font-family: "Oswald" , sans-serif;
    margin-bottom: 20px;
  }
}
</style>