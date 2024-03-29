<template>
  <div class="container">
    <template v-if="loading">
      <div class="skeletons">
        <div class="skeleton poster"></div>
        <div class="specs">
          <div class="skeleton title"></div>
          <div class="skeleton spec"></div>
          <div class="skeleton plot"></div>
          <div class="skeleton etc"></div>
          <div class="skeleton etc"></div>
          <div class="skeleton etc"></div>
        </div>
      </div>
      <Loader 
        :size="3" 
        :z-index="9"
        :fixed="true" />
    </template>

    <!-- 영화 상세 정보내용 출력 -->
    <div 
      v-else
      class="movie-details">
      <div
        :style="{ backgroundImage: `url(${requestDiffSizeImage(theMovie.Poster)})` }"
        class="poster">
        <Loader 
          v-if="imageLoading"
          absolute />
      </div>
      <div class="specs">
        <div class="title">
          {{ theMovie.Title }}
        </div>
        <div class="labels">
          <span>{{ theMovie.Released }}</span>
          <span>{{ theMovie.Runtime }}</span>
          <span>{{ theMovie.Country }}</span>
        </div>
        <div class="plot">
          {{ theMovie.Plot }}
        </div>
        <div class="ratings">
          <h3>Ratings</h3>
          <div class="rating-wrap">
            <div 
              v-for="{ Source: name, Value: score } in theMovie.Ratings"
              :key="name"
              :title="name"
              class="rating">
              <img 
                :src="`https://raw.githubusercontent.com/ParkYoungWoong/vue3-movie-app/master/src/assets/${name}.png`" 
                :alt="name" />
              <span>{{ score }}</span>
            </div>
          </div>
        </div>
        <div>
          <h3>Actors</h3>
          {{ theMovie.Actors }}
        </div>
        <div>
          <h3>Director</h3>
          {{ theMovie.Director }}
        </div>
        <div>
          <h3>Production</h3>
          {{ theMovie.Production }}
        </div>
        <div>
          <h3>Genre</h3>
          {{ theMovie.Genre }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import Loader from '~/components/Loader'
export default {
  components : {
    Loader
  },
  data() {
    return {
      imageLoading: true
    }
  },
  computed: {
    // Vuex helpers 적용
    ...mapState('movie', [
      'theMovie',
      'loading'
    ])
  },
  created() {
    console.log(this.$route)
    this.$store.dispatch('movie/searchMovieWithId', {
      id: this.$route.params.dynamicID 
    })
  },
  methods: {
    requestDiffSizeImage(url, size = 700) {
      // 영화 포스터가 없는 경우 예외처리
      if (!url || url === 'N/A') {
        this.imageLoading = false
        return ''
      }
      const src = url.replace('SX300', `SX${size}`)
      this.$loadImage(src)
        .then(() => {
          this.imageLoading = false
        })
        return src
    }
  }
}
</script>

<style lang="scss" scoped>

.container {
  padding-top: 40px;
}
.skeletons {
  display: flex;
  .poster {
    flex-shrink: 0;
    width: 500px;
    height: 500px * 3 / 2;
    margin-right: 70px;
  }
  .specs {
    flex-grow: 1;
  }
  .specs .skeleton:nth-child(n+2) {
    margin-top: 20px;
  }
  .skeleton {
    border-radius: 10px;
    background-color: $gray-200;
    
    &.title {
      width: 80%;
      height: 70px;
    }
    &.spec {
      width: 60%;
      height: 30px;
    }
    &.plot {
      width: 100%;
      height: 250px;
    }
    &.etc {
      width: 50%;
      height: 50px;
    }
  }
}

.movie-details {
  display: flex;
  color: $gray-600;
  .poster {
    flex-shrink: 0;
    width: 500px;
    height: 500px * 3 / 2;
    margin-right: 70px;
    border-radius: 10px;
    background-color: $gray-200;
    background-size: cover;
    background-position: center;
    position: relative;
  }
  .specs {
    flex-grow: 1;
    .title {
      color: $black;
      font-family: 'Oswald', sans-serif;
      font-size: 70px;
      line-height: 1;
      margin-bottom: 30px;
    }
    .labels {
      color: $primary;
      span { // 영화 출시년도, 상영시간, 국가
        &::after {
          content: "\00b7"; // 가운데 점표시 (특수문자)
          margin: 0 6px;
        }
        &:last-child::after {
          display: none;

        }
      }
    }
    .plot {
      margin-top: 20px;
    }
    .ratings {
      .rating-wrap {
        display: flex;
        .rating {
          display: flex;
          align-items: center;
          margin-right: 32px;
          img {
            height: 30px; /* 출력되는 이미지의 비율은 유지하고 높이값만으로 이미지 사이즈 크기를 제어 */
            flex-shrink: 0;
            margin-right: 6px;
          }
        }
      }
    }
    h3 {
      margin: 24px 0 6px;
      color: $black;
      font-family: "Oswald", sans-serif;
      font-size: 20px;
    }
  }
  // 뷰포트의 너비가 Extra-large 보다 작을경우
  @include media-breakpoint-down(xl) {
    .poster {
      width: 300px;
      height: 300px * 3 / 2;
      margin-right: 40px;
    }
  }
  @include media-breakpoint-down(lg) { // largh
      display: block;
      .poster {
        margin-bottom: 40px;
      }
  }
  @include media-breakpoint-down(md) { // medium
    .specs {
      .title { // 영화 제목
        font-size: 50px;
      }
      .ratings { // 평점
        .rating-wrap {
          display: block;
          .rating {
            margin-top: 10px;
          }
        }
      }
    }
  }
}

</style>