<template>
  <section class="main-slider w-full text-white">
    <no-ssr>
      <carousel ref="carousel" :per-page="1">
        <slide v-for="(slide, index) in slides" :key="index">
          <div class="container w-100">
            <figure class="slideshow-slide-image">
              <img :src="slide.image" />
            </figure>
            <div class="slide-content flex items-center justify-center">
              <div class="w-full px-10p slideshow-slide-info">
                <h1
                  class="text-hero mt-0 mb-8 text-left"
                  data-testid="mainSliderTitle"
                >
                  {{ slide.title }}
                </h1>
                <p
                  class="text-hero mt-0 mb-8 text-center"
                  data-testid="mainSliderTitle"
                >
                  {{ slide.subtitle }}
                </p>
                <div class="mx-auto w-48">
                  <a :href="slide.link" class="btn-primary">
                    {{ slide.button_text }}
                  </a>
                </div>
              </div>
            </div>
          </div>
          <div class="flickity-button">
            <button
              @click.prevent="prevSlide"
              class="flickity-prev-next-button previous"
              type="button"
              aria-label="Previous"
            >
              <svg class="flickity-button-icon" viewBox="0 0 100 100">
                <path
                  d="M 10,50 L 60,100 L 70,90 L 30,50  L 70,10 L 60,0 Z"
                  class="arrow"
                ></path>
              </svg>
            </button>
            <button
              @click.prevent="nextSlide"
              class="flickity-prev-next-button next"
              type="button"
              aria-label="Next"
            >
              <svg class="flickity-button-icon" viewBox="0 0 100 100">
                <path
                  d="M 10,50 L 60,100 L 70,90 L 30,50  L 70,10 L 60,0 Z"
                  class="arrow"
                  transform="translate(100, 100) rotate(180) "
                ></path>
              </svg>
            </button>
          </div>
        </slide>
      </carousel>
    </no-ssr>
  </section>
</template>

<script>
import NoSSR from "vue-no-ssr";
import sliderData from "theme/resource/slider.json";
import ButtonFull from "theme/components/theme/ButtonFull";

export default {
  data() {
    return {
      currentSlide: 1,
      slides: [],
      totalSlides: 2,
    };
  },
  components: {
    ButtonFull,
    Carousel: () => import("vue-carousel").then((Slider) => Slider.Carousel),
    Slide: () => import("vue-carousel").then((Slider) => Slider.Slide),
    "no-ssr": NoSSR,
  },
  methods: {
    updateSliderData(data) {
      this.slides = data.slides;
      this.totalSlides = data.total;
    },
    nextSlide() {
      this.$refs.carousel.goToPage(this.$refs.carousel.currentPage + 1);
    },
    prevSlide() {
      this.$refs.carousel.handleNavigation("backward");
    },
  },
  mounted() {
    setInterval(() => {
      this.currentSlide = (this.currentSlide + 1) % this.totalSlides;
    }, 5000);
  },
  created() {
    this.updateSliderData(sliderData);
  },
};
</script>
<style lang="scss">
.main-slider {
  width: 100%;
  padding-bottom: 0;
  .VueCarousel-pagination {
    display: none;
  }
}
</style>
<style scoped>
.main-slider {
  height: 640px;
}
.slide {
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
}
.slide-content {
  height: 640px;
}
.slideshow-slide-image img {
  display: block;
  width: 100%;
  height: auto;
  object-fit: cover;
}
.flickity-button {
  margin-top: -10%;
}
.flickity-prev-next-button {
  position: absolute;
  bottom: 0;
  display: block;
  width: 10%;
  height: 0;
  padding-bottom: 10%;
  cursor: pointer;
  background-color: #ffffff;
  border: 0;
  fill: #292929;
}
.flickity-prev-next-button svg {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 39px;
  height: 39px;
  transform: translate(-50%, -50%);
}
.flickity-prev-next-button.previous {
  right: 10%;
}
.flickity-prev-next-button.next {
  right: 0;
}
.slideshow-slide-info {
  position: relative;
  width: 90%;
  padding: 30px 30px 30px 1px;
  margin-top: -50px;
  margin-left: -1px;
  background-color: #ffffff;
}
@media screen and (min-width: 640px) {
  .slideshow-slide-info {
    margin-top: 15%;
  }
  .slideshow-slide-image {
    float: right;
    width: auto;
    max-width: 58%;
    display: block;
    margin: 0;
    overflow: hidden;
  }
}
@media (max-width: 75em) {
  .slide-content {
    height: 400px;
  }
}
@media (max-width: 64em) {
  .slideshow-slide-info h1 {
    font-size: 30px;
  }
  .main-slider {
    height: 559px;
  }
  .slide {
    background-position: left;
  }
  .slide-content {
    height: 359px;
  }
}
</style>