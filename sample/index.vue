<template>
  <div class="sample">
    <tab-carousel
        v-bind:items="items"
        v-bind:page="this.carouselPage"
        v-bind:transformXPercent="this.carouselTransformXPercent"/>
    <carousel
      class="carousel"
      v-bind:length="items.length"
      v-bind:startPage="this.carouselPage"
      v-bind:onPageUpdate="updateCarouselPage"
      v-bind:onCarouselMove="touchModeCarousel">
      <div
        class="content"
        v-bind:style="{backgroundColor: item.color}"
        v-for="(item, index) in items" v-bind:key="index">
        {{item.name}}
      </div>
    </carousel>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

import Carousel from '../src/carousel.vue'
import TabCarousel from '../src/tab-carousel.vue'

export default Vue.extend({
  data: () => {
    return {
      items: [{
        name: 'テスト1',
        color: '#DA5019'
      }, {
        name: 'テスト2',
        color: '#009250'
      }, {
        name: 'テスト3',
        color: '#007AB7'
      }, {
        name: 'テスト4',
        color: '#F6CA06'
      }, {
        name: 'テスト5',
        color: '#0086AB'
      }],
      carouselPage: 0
    };
  },
  components: {
    'carousel': Carousel,
    'tab-carousel': TabCarousel,
  },
  methods: {
    updateCarouselPage: function (page) {
      this.carouselTransformXPercent = null;
      this.carouselPage = page;
    },
    touchModeCarousel: function (percent) {
      this.carouselTransformXPercent = percent;
    }
  },
  mounted () {
    this.carouselPage = 2;
  }
});
</script>

<style scoped>

  .sample {
    width: 100%;
    height: 100%;
    overflow: hidden;
    position: relative;
    display: flex;
    flex-direction: column;
  }

  .carousel {
    display: flex;
    flex: 1;
  }

  .content {
    min-width: 100%;
    color: white;
    overflow-x: hidden;
    overflow-y: scroll;
    -webkit-overflow-scrolling: touch;
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>


