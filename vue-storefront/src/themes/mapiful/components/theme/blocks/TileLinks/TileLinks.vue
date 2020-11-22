<template>
  <div class="row center-xs">
    <div
      class="col-sm-4 pb15"
      v-for="(tile, index) in social_tiles"
      :key="index"
    >
      <div v-if="tile.image" class="tile center-xs middle-xs">
        <a :href="tile.link">
          <img class="tile-image" v-lazy="tile.image" :alt="tile.alt" />
          <span class="ds-button">{{ tile.text }}</span></a
        >
      </div>
      <div v-else class="left">
        <h4>{{ tile.text }}</h4>
        <p>{{ tile.alt }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "TileLinks",
  data() {
    return {
      social_tiles: [
        {
          image: "/assets/ig/ig01.jpg",
          alt: "Woman holding in a map on the wall",
          text: "Shop Maps",
          link: "/category/maps",
        },
        {
          image: "/assets/ig/ig02.jpg",
          alt: "Two hands touching a star print",
          text: "Shop Stars",
          link: "/category/stars",
        },
        {
          image: "/assets/ig/ig03.jpg",
          alt: "A zodiac print with books around it",
          text: "Shop Zodiacs",
          link: "/category/zodiacs",
        },
        {
          alt:
            "Customize these meaningful gifts. With Mapiful you are the designer. Tell your stories your way, always.",
          text: "Tell your story",
          link: "/category/textart",
        },
        {
          alt:
            "You are unique and there is no reason that your home and art should not reflect that too.",
          text: "You are the creative",
          link: "/category/textart",
        },
        {
          alt:
            "We use high quality, museum graded paper and print with inks that withstand the tests of time.",
          text: "We take pride in quality",
          link: "/category/textart",
        },
        {
          image: "/assets/ig/ig04.jpg",
          alt: "A woman holding up a text art print",
          text: "Shop Text Art",
          link: "/category/textart",
        },
        {
          image: "/assets/ig/ig05.jpg",
          alt: "A black frame standing up",
          text: "Shop Frames",
          link: "/category/frames",
        },
        {
          image: "/assets/ig/ig06.jpg",
          alt: "A white frame hanger",
          text: "Shop Hangers",
          link: "/category/hangers",
        },
      ],
    };
  },
};
</script>

<style lang='scss' scoped>
@import "~theme/css/animations/transitions";

.ds-button {
  position: absolute;
  left: 0;
  width: 40%;
  background: white;
  bottom: 0;
  padding: 20px 0;
}
.tile {
  display: flex;
  overflow: hidden;
}

.tile-image {
  width: 100%;
  height: 100%;
  transition: transform 0.3s $motion-main;

  &:hover,
  &:focus {
    transform: scale(1.1);
  }
}
</style>
