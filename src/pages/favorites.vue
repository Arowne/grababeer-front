<template>
  <q-page class="q-mt-sm" style="width: 70%; margin-left: 15%">
    <h1> Favorites </h1>
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
                  @click="$router.push('/details/' + String(beer.id))"
                  v-for="beer in beers"
                  v-if="favoriteList.indexOf(String(beer.id)) != -1"
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
      beers: [],
      favoriteList: []
    };
  },
  beforeMount() {
    this.getBeers();
    this.listFavorite();
  },
  mounted() {
    console.log(this.favoriteList);
  },
  methods: {
    getBeers () {
      axios.get('https://api.punkapi.com/v2/beers')
      .then((response) => {
        this.beers = response.data
        console.log(response.data) 
      })
    },
    listFavorite () {
      let token = localStorage.getItem("aic-session-token");
      
      axios({
        method: "get", //you can set what request you want to be
        url: "http://localhost:9000/beer",
        headers: {
          Authorization: "Bearer " + token,
        },
      }).then((response) => {
          let favoriteList = [];
          
          for (let index = 0; index < response.data.length; index++) {
            const element = response.data[index];
            favoriteList.push(element.api_id);
          }
          this.favoriteList = favoriteList;
        })
        .catch((error) => {
          console.log(error)
        })
    },
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
