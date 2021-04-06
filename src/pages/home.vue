<template>
  <q-page class="q-mt-sm" style="width: 70%; margin-left: 15%">
    <div class="row q-col-gutter-sm">
      <div class="col-lg-8 col-md-8 col-sm-8 col-xs-12">
        <q-carousel
          :class="$q.platform.is.desktop ? 'q-ml-sm' : ''"
          arrows
          animated
          v-model="slide"
          height="400px"
        >
          <q-carousel-slide name="first" :key="beers[1].id" :img-src="beers[1].image_url">
            <a :href="'/details/' + beers[1].id">
              <div class="absolute-bottom custom-caption">
                <div class="text-subtitle1" @click="$router.push('/details/' + beers[1].id)" >{{beers[1].name}}</div>
                <div class="text-caption">{{beers[1].tagline}}</div>
              </div>
            </a>
          </q-carousel-slide>
          <q-carousel-slide name="second" :key="beers[3].id" :img-src="beers[3].image_url">
            <a :href="'/details/' + beers[3].id">
              <div class="absolute-bottom custom-caption">
                <div class="text-subtitle1" @click="$router.push('/details/' + beers[3].id)">{{beers[3].name}}</div>
                <div class="text-caption">{{beers[3].tagline}}</div>
              </div>
            </a>
          </q-carousel-slide>
          <q-carousel-slide name="third" :key="beers[4].id" :img-src="beers[4].image_url">
            <a :href="'/details/' + beers[4].id">
              <div class="absolute-bottom custom-caption">
                <div class="text-subtitle1">{{beers[4].name}}</div>
                <div class="text-caption">{{beers[4].tagline}}</div>
              </div>
            </a>
          </q-carousel-slide>
        </q-carousel>
      </div>
      <div class="col-lg-4 col-md-4 col-sm-4 col-xs-12">
        <q-carousel
          :class="$q.platform.is.desktop ? 'q-ml-sm' : ''"
          v-model="slide"
          height="400px"
        >
          <q-carousel-slide name="first" :key="beers[5].id" :img-src="beers[5].image_url">
            <a :href="'/details/' + beers[5].id">
              <div class="absolute-bottom custom-caption">
                <div class="text-subtitle1">{{beers[5].name}}</div>
                <div class="text-caption">{{beers[5].tagline}}</div>
              </div>
            </a>
          </q-carousel-slide>
        </q-carousel>
      </div>
    </div>
    <div class="text-grey-9 text-weight-bold">
      <div class="row items-center q-mx-sm">
        <div class="col-12 q-mt-sm">
          <div class="q-pl-md bg-white q-pt-sm ">
            <span class="text-grey-9 text-h6 text-weight-bold">Latest Launches</span>
            <a class="text-primary q-ml-sm cursor-pointer">[see all]</a>
          </div>
          <q-carousel
            v-model="latest_slide"
            transition-prev="slide-right"
            transition-next="slide-left"
            swipeable
            animated
            control-color="primary"
            navigation
            padding
            arrows
            height="260px"
            class="rounded-borders"
          >
            <q-carousel-slide
              v-for="(val, idx) in [1, 2, 3]"
              :name="val"
              :key="idx"
              class="column no-wrap"
            >
              <div class="row fit justify-start items-center q-gutter-xs q-col-gutter no-wrap">
                <div
                  v-for="beer in beers"
                  @click="$router.push('/details/' + beer.id)"
                  class="col-lg-2 col-md-2 col-sm-12 col-xs-12 hover_border_grey text-center full-height"
                >
                  <q-img
                    style="height: 100px"
                    class="rounded-borders"
                    :src="beer.image_url"
                  >
                  </q-img>
                  <div>{{beer.name}}</div>
                  <div class="text-caption text-weight-bold text-green">{{beer.tagline}}</div>
                  <div>
                    <span class="q-ml-sm text-grey-6"
                      >{{beer.ph}} %</span
                    >
                  </div>
                </div>
              </div>
            </q-carousel-slide>
          </q-carousel>
        </div>
      </div>

    </div>
  </q-page>
</template>

<script>
import Vue from "vue";
import axios from "axios";

export default {
  data() {
    return {
      slide: "first",
      trending_slide: 1,
      latest_slide: 1,
      beers: []
    };
  },
  beforeMount() {
    this.getBeers();
  },
  methods: {
    getBeers () {
      axios.get('https://api.punkapi.com/v2/beers')
      .then((response) => {
        this.beers = response.data
        console.log(response.data) 
      })
    }
  },
};
</script>

<style lang="sass" scoped>
.custom-caption
  text-align: center
  padding: 8px
  color: white
  background-color: rgba(0, 0, 0, .3)
</style>
