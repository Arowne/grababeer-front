<template>
  <div>
    <div class="row bg-white q-mt-sm p-5 m-5" style="height: 100%">
      <div class="col-lg-5 col-md-5 col-sm-12 col-xs-12">
        <div class="q-pa-md">
          <img
            :src="beer[0].image_url"
            style="height: 600px; width: 200px; margin-left: 30%"
          />
          <div class="q-mt-md" style="margin-left: 30%">
            <q-btn
              class="q-mt-md"
              color="orange-9"
              @click="addToFavorite"
              v-if="!isFavorite"
              icon="favorite"
              label="Add to favorite"
            />

            <q-btn
              v-if="isFavorite"
              class="q-mt-md"
              color="red-9"
              @click="removeFromFavorite"
              icon="favorite"
              label="Remove from favorite"
            />
          </div>
        </div>
      </div>
      <div class="col-lg-7 col-md-7 col-sm-12 col-xs-12">
        <!--<q-scroll-area :style="{'height':(win_height-200)+'px'}">-->
        <div class="row">
          <div
            class="col-lg-7 col-md-7 col-sm-12 col-xs-12"
            :class="$q.platform.is.desktop ? '' : 'q-px-md'"
          >
            <div class="text-subtitle1 text-grey-10 q-mt-sm q-pt-xs">
              <h2>{{ beer[0].name }}</h2>
            </div>
            <div>
              <div class="text-caption text-green-8 text-weight-bolder q-mt-sm">
                Alcool percent
              </div>
              <span class="text-h6">{{ beer[0].abv }} %</span>
            </div>
            <div class="q-mt-sm text-weight-bold">Description</div>
            <div class="q-mt-sm mb-5" style="margin-bottom: 5%">
              <div class="text-subtitle2 text-grey-10 q-mt-sm">
                {{ beer[0].description }}
              </div>
            </div>
            <i>ibu</i>
            <p>{{ beer[0].ibu }}</p>

            <div class="row">
              <div class="col-md-6">
                <div class="q-mt-sm text-weight-bold">Malts</div>
                <div class="q-mt-sm mb-5" style="margin-bottom: 5%">
                  <div
                    class="text-subtitle2 text-grey-10 q-mt-sm"
                    v-for="malt in beer[0].ingredients.malt"
                  >
                    {{ malt.name }}
                  </div>
                </div>
              </div>
              <div class="col-md-6">
                <div class="q-mt-sm text-weight-bold">Hops</div>
                <div class="q-mt-sm mb-5" style="margin-bottom: 5%">
                  <div
                    class="text-subtitle2 text-grey-10 q-mt-sm"
                    v-for="hops in beer[0].ingredients.hops"
                  >
                    {{ hops.name }}
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!--</q-scroll-area>-->
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "details.vue",
  data() {
    return {
      slide: 1,
      tab: "Specifications",
      rating_point: 4.3,
      rat_5: 5,
      rat_4: 4,
      rat_3: 3,
      rat_2: 2,
      rat_1: 1,
      beer: null,
      favoritesList: [],
      isFavorite: false,
    };
  },
  mounted() {
    this.getBeer();
  },
  methods: {
    getBeer() {
      axios
        .get("https://api.punkapi.com/v2/beers/" + this.$route.params.id)
        .then((response) => {
          this.beer = response.data;
          this.listFavorite();
        });
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
          for (let index = 0; index < response.data.length; index++) {
            const element = response.data[index];

            if (element.api_id == this.beer[0].id) {
              this.isFavorite = true
            }

          }
        })
        .catch((error) => {
          console.log(error.response.data)
        })
    },
    addToFavorite() {
      let hopsList = this.beer[0].ingredients.hops;
      let maltsList = this.beer[0].ingredients.malt;

      let hops = [];
      let malts = [];

      for (let index = 0; index < hopsList.length; index++) {
        const element = hopsList[index].name;
        hops.push("'" + element + "'");
      }

      for (let index = 0; index < maltsList.length; index++) {
        const element = maltsList[index].name;
        malts.push("'" + element + "'");
      }

      let token = localStorage.getItem("aic-session-token");
      axios({
        method: "post", //you can set what request you want to be
        url: "http://localhost:9000/beer",
        data: {
            beer_id: this.$route.params.id,
            malts: "[" + String(malts) + "]",
            hops: "[" + String(hops) + "]",
          },
        headers: {
          Authorization: "Bearer " + token,
        },
      }).then((response) => {
          console.log(response);
          this.isFavorite = true;
          // this.beer = response.data;
        })
        .catch((error) => {
          console.log(error.response.data)
        })
    },
    removeFromFavorite() {
      let hopsList = this.beer[0].ingredients.hops;
      let maltsList = this.beer[0].ingredients.malt;

      let hops = [];
      let malts = [];

      for (let index = 0; index < hopsList.length; index++) {
        const element = hopsList[index].name;
        hops.push("'" + element + "'");
      }

      for (let index = 0; index < maltsList.length; index++) {
        const element = maltsList[index].name;
        malts.push("'" + element + "'");
      }

      let token = localStorage.getItem("aic-session-token");
      axios({
        method: "delete", //you can set what request you want to be
        url: "http://localhost:9000/beer",
        data: {
            beer_id: this.$route.params.id,
          },
        headers: {
          Authorization: "Bearer " + token,
        },
      }).then((response) => {
          this.isFavorite = false;
          // this.beer = response.data;
        })
        .catch((error) => {
          console.log(error.response.data)
        })
    },
  },
  computed: {
    win_width() {
      return this.$q.screen.width - 59;
    },
    win_height() {
      return this.$q.screen.height - 0;
    },
  },
};
</script>

<style scoped>
</style>
