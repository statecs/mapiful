<template>
  <section class="main-slider w-full text-white">
    <no-ssr>
      <carousel ref="carousel" :per-page="1">
        <slide v-for="(slide, index) in slides" :key="index">
          <div class="container w-100">
            <div
              data-testid="review_carousel_main_slider_wrapper"
              tabindex="-1"
              style="width: 100%; display: inline-block"
            >
              <div class="css-12u4t8w">
                <h5 class="css-fmz1tz" data-testid="review_carousel_quote">
                  {{ slide.subtitle }}
                </h5>
              </div>
            </div>
          </div>
        </slide>
      </carousel>
    </no-ssr>
  </section>
</template>

<script>
import NoSSR from "vue-no-ssr";
import sliderData from "theme/resource/review-slider.json";
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
.slide {
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
}
.slideshow-slide-image img {
  display: block;
  width: 100%;
  height: auto;
  object-fit: cover;
}
.css-12u4t8w {
  padding: 0rem 10.125vw;
}
.css-fmz1tz::before {
  content: "“";
  position: absolute;
  left: -0.6rem;
}
.css-fmz1tz {
  font-size: 1.2rem;
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