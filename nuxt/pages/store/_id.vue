<template>
  <div class="store-info">
    <div>
      <v-breadcrumbs :items="breadcrumb">
        <template v-slot:divider>
          <v-icon>mdi-chevron-right</v-icon>
        </template>
      </v-breadcrumbs>
    </div>
    <v-container>
      <v-row justify="center" v-if="selectedStore">
        <v-col cols="11" md="9" class="pa-0">
          <v-row justify="center">
            <v-col
              cols="12"
              :sm="hasExternal ? 8 : 12"
              :md="hasExternal ? 9 : 12"
              class="pa-0 px-3"
            >
              <v-card class="pa-0 mb-3">
                <v-img
                  :src="`${baseURL}thumbnails/${selectedStore.id}.png`"
                  class="text-right"
                  max-height="500px"
                  aspect-radio="1.6"
                  position="top center"
                >
                  <v-chip
                    v-if="isNewStore(selectedStore)"
                    color="green"
                    text-color="white"
                    class="ma-2"
                    >New</v-chip
                  >
                  <v-tooltip bottom>
                    <template v-slot:activator="{ on }">
                      <v-chip
                        v-if="selectedStore.trending > 0"
                        color="purple"
                        text-color="white"
                        v-on="on"
                      >
                        {{ selectedStore.trending }}%

                        <v-icon v-on="on" pr-2 small right>fa-fire</v-icon>
                      </v-chip>
                    </template>
                    <span>Trending score</span>
                  </v-tooltip>

                  <v-chip
                    v-if="hasNewComment(selectedStore)"
                    color="blue"
                    text-color="white"
                    class="ma-2"
                    >New comment</v-chip
                  >
                </v-img>
                <v-row class="pa-5">
                  <v-col class="pb-1">
                    <div class="headline">
                      <h3>
                        <a class="" @click.stop :href="selectedStore.href"
                          >{{ selectedStore.name }}

                          <v-icon class="ml-1" color="blue darken-2"
                            >mdi-open-in-new</v-icon
                          >
                        </a>
                      </h3>
                    </div>
                    <edit-tags :store="selectedStore"></edit-tags>
                    <v-row class="pt-0">
                      <v-col>
                        <p>
                          {{ selectedStore.description }}
                        </p>
                        <!-- <v-layout row pb-3><a @click.stop :href="selectedStore.href">Visit website</a></v-layout> -->

                        <div
                          v-if="
                            selectedStore.uri &&
                            selectedStore.uri.toLowerCase() != 'unknown'
                          "
                          class="px-0"
                        >
                          <span class="break-word"
                            ><b>Node:&nbsp;</b
                            ><a
                              :href="
                                'https://1ml.com/node/' +
                                selectedStore.uri.split('@')[0]
                              "
                              >{{ selectedStore.uri }}</a
                            ></span
                          >
                        </div>

                        <div
                          v-if="
                            selectedStore.digital_goods &&
                            selectedStore.digital_goods.length > 0
                          "
                          class="px-0"
                        >
                          <b>Digital goods:&nbsp;</b
                          >{{ selectedStore.digital_goods }}
                        </div>

                        <div
                          v-if="
                            selectedStore.sector &&
                            selectedStore.sector.length > 0
                          "
                          class="px-0"
                        >
                          <b>Sector:&nbsp;</b
                          ><router-link
                            :to="
                              '/?sector=' +
                              encodeURIComponent(selectedStore.sector)
                            "
                            >{{ selectedStore.sector }}</router-link
                          >
                        </div>

                        <div
                          v-if="
                            selectedStore.sector &&
                            selectedStore.sector.length > 0
                          "
                          class="px-0"
                        >
                          <b>Date added:&nbsp;</b
                          ><span v-if="selectedStore.added">
                            {{
                              new Date(
                                selectedStore.added * 1000
                              ).toLocaleDateString('en-GB', {
                                year: 'numeric',
                                month: 'long',
                                day: 'numeric',
                              })
                            }}</span
                          ><a v-else>24/Feb/2018</a>
                        </div>

                        <div
                          v-if="
                            selectedStore.sector &&
                            selectedStore.sector.length > 0
                          "
                          class="px-0"
                        >
                          <b>Lifetime score: &nbsp;</b
                          ><span v-if="selectedStore.lifetime">
                            {{ selectedStore.lifetime }}</span
                          ><span v-else>0</span>
                        </div>
                      </v-col>
                    </v-row>

                    <v-row>
                      <v-col>
                        <vote-line
                          :store="selectedStore"
                          :isInfo="true"
                        ></vote-line>
                      </v-col>
                    </v-row>

                    <v-row class="pl-2 pr-2 pt-3">
                      <v-col cols="6">
                        <v-btn
                          text
                          icon
                          color="blue"
                          :disabled="!selectedStore.social.twitter"
                          :href="getSocialHref(selectedStore.social.twitter)"
                        >
                          <v-icon>fab fa-twitter</v-icon>
                        </v-btn>

                        <v-btn
                          text
                          icon
                          color="blue darken-3"
                          :disabled="!selectedStore.social.facebook"
                          :href="getSocialHref(selectedStore.social.facebook)"
                        >
                          <v-icon>fab fa-facebook</v-icon>
                        </v-btn>

                        <v-btn
                          text
                          icon
                          color="orange darken-2"
                          :disabled="!selectedStore.social.reddit"
                          :href="getSocialHref(selectedStore.social.reddit)"
                        >
                          <v-icon>fab fa-reddit</v-icon>
                        </v-btn>
                      </v-col>

                      <v-col cols="6">
                        <v-row justify="end">
                          <embed-modal
                            :store="selectedStore"
                            :baseURL="baseURL"
                            class="ml-2"
                          ></embed-modal>
                          <edit-store-modal
                            :store="selectedStore"
                            class="ml-2"
                          ></edit-store-modal>
                          <ban-store-modal
                            :store="selectedStore"
                            class="ml-2"
                          ></ban-store-modal>
                        </v-row>
                      </v-col>
                    </v-row>
                  </v-col>
                </v-row>
              </v-card>
            </v-col>
            <v-col v-if="hasExternal" cols="12" sm="4" md="3" class="pa-0">
              <div class="ma-3 headline font-weight-medium external-title">
                External
              </div>

              <v-card
                v-for="(
                  external, propertyName, index
                ) in selectedStore.external"
                :key="index"
                class="mx-3 mb-3 py-2"
                :href="external.href"
              >
                <v-layout row class="py-2">
                  <v-flex shrink>
                    <v-img
                      :src="`https://lightningnetworkstores.com/external/${propertyName}.svg`"
                      class="external-image"
                    >
                    </v-img>
                  </v-flex>
                  <v-flex grow class="external-text">
                    <b>{{ propertyName }}</b>
                  </v-flex>
                </v-layout>
              </v-card>
            </v-col>
          </v-row>
          <v-container v-if="relatedStores.length > 0" class="pa-0 pt-4">
            <v-layout class="mt-4 mb-2" justify-center>
              <h1>Similar Stores</h1>
            </v-layout>
            <v-row no-gutters justify="center">
              <v-col cols="12" sm="8" md="7" xl="4">
                <store-card
                  class="mb-4"
                  v-for="store in relatedStores.slice(0, maxSimilarToShow)"
                  :key="'store-' + store.id"
                  :store="store"
                >
                </store-card>
              </v-col>
            </v-row>
            <v-layout justify-center="true" v-if="relatedStores.length > 1">
              <v-btn @click="toggleMoreSimilar()" color="primary">{{
                maxSimilarToShow > 1 ? 'Hide Similar' : 'Show more'
              }}</v-btn>
            </v-layout>
          </v-container>

          <v-card class="my-8 pa-2">
            <v-card-title primary-title class="pa-3">
              <div>
                <div class="headline font-weight-medium">Reviews</div>
              </div>
            </v-card-title>
            <v-card-text class="body-1">
              <v-row>
                <v-flex px-3 pb-3
                  >To leave a review, up or downvote the selectedStore.</v-flex
                >
              </v-row>
              <v-row pa-2 class="text-center">
                <v-flex grow justify-center pa-3
                  ><v-btn
                    fab
                    @click="filter('positive')"
                    :outlined="currentFilter !== 'positive'"
                    color="success"
                    class="mb-2"
                    ><v-icon
                      :color="currentFilter == 'positive' ? 'white' : 'success'"
                      large
                      >mdi-thumb-up</v-icon
                    ></v-btn
                  >
                  <h4>
                    Positive:
                    {{
                      selectedStore.comments.filter(
                        (comment) =>
                          comment.parent == 'null' && comment.score > 0
                      ).length
                    }}
                  </h4>
                </v-flex>
                <v-flex grow justify-center pa-3
                  ><v-btn
                    fab
                    @click="filter('all')"
                    :outlined="currentFilter !== 'all'"
                    color="blue"
                    class="mb-2"
                    ><v-icon
                      :color="currentFilter == 'all' ? 'white' : 'blue'"
                      large
                      >mdi-thumbs-up-down</v-icon
                    ></v-btn
                  >
                  <h4>
                    All:
                    {{
                      selectedStore.comments.filter(
                        (comment) => comment.parent == 'null'
                      ).length
                    }}
                  </h4></v-flex
                >
                <v-flex grow justify-center pa-3
                  ><v-btn
                    fab
                    @click="filter('negative')"
                    :outlined="currentFilter !== 'negative'"
                    color="error"
                    class="mb-2"
                    ><v-icon
                      :color="currentFilter == 'negative' ? 'white' : 'error'"
                      large
                      >mdi-thumb-down</v-icon
                    ></v-btn
                  >
                  <h4>
                    Negative:
                    {{ selectedStore.comments.filter(comment => comment.parent == "null" && comment.score &lt; 0).length }}
                  </h4></v-flex
                >
              </v-row>
            </v-card-text>
          </v-card>

          <Review
            v-for="comment in comments"
            :key="comment.id"
            :comment="comment"
            :comments="selectedStore.comments"
            :store="selectedStore"
          ></Review>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import StoreCard from '~/components/StoreCard.vue'
export default {
  components: { StoreCard },
  head() {
    return {
      title: this.selectedStore.name + ' | Lightning Network Stores',
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: this.selectedStore.description,
        },
        {
          hid: 'og:title',
          property: 'og:title',
          content: this.selectedStore.name + ' | Lightning Network Stores',
        },
        {
          hid: 'og:description',
          property: 'og:description',
          content: this.selectedStore.description,
        },
        {
          hid: 'twitter:title',
          property: 'twitter:title',
          content: this.selectedStore.name + ' | Lightning Network Stores',
        },
        {
          hid: 'twitter:description',
          property: 'twitter:description',
          content: this.selectedStore.description,
        },
        {
          hid: 'og:image',
          property: 'og:image',
          content: '/thumbnails/' + this.selectedStore.id + '_0.png',
        },
      ],
    }
  },
  data() {
    return {
      breadcrumb: [],
      store: null,
      currentFilter: 'all',
      maxSimilarToShow: 1,
    }
  },
  async asyncData({ params, store }) {
    const store_id = params.id

    const selectedStore = await store.dispatch('getStore', { id: store_id })
    let comments = selectedStore.comments

    return { selectedStore, comments }
  },

  mounted() {
    // this.setMetaTags()
    // this.comments = this.selectedStore.comments
    //   .filter((comment) => comment.parent == 'null')
    //   .sort((a, b) => {
    //     if (Math.abs(b.score) !== Math.abs(a.score)) {
    //       return Math.abs(b.score) - Math.abs(a.score)
    //     }
    //     return b.timestamp - a.timestamp
    //   })
    // this.comments.sort((a, b) => Math.abs(b.score) - Math.abs(a.score))
    this.breadcrumb = [
      {
        text: 'Stores',
        disabled: false,
        href: '/',
      },
      {
        text: this.selectedStore.name,
        disabled: false,
        href: location.href,
      },
    ]
  },
  computed: {
    baseURL() {
      return this.$store.state.baseURL
    },
    hasExternal() {
      return Object.keys(this.selectedStore.external).length > 0
    },
    relatedStores() {
      //Filters the same store id
      return this.selectedStore.related.filter(
        (store) => store.id !== this.selectedStore.id
      )
    },
  },

  methods: {
    toggleMoreSimilar() {
      this.maxSimilarToShow =
        this.maxSimilarToShow !== 1 ? 1 : this.relatedStores.length
    },
    getSocialHref(social) {
      if (social && social.href) return social.href

      return ''
    },
    isNewStore() {
      return (
        new Date(this.selectedStore.added * 1000 + 1000 * 60 * 60 * 24 * 8) >
        new Date()
      )
    },
    hasNewComment(store) {
      return (
        new Date(this.selectedStore.last_commented + 1000 * 60 * 60 * 24 * 8) >
        new Date()
      )
    },
    filter(filter) {
      this.currentFilter = filter
      switch (filter) {
        case 'all':
          this.comments = this.selectedStore.comments.filter(
            (comment) => comment.parent == 'null'
          )
          break
        case 'negative':
          this.comments = this.selectedStore.comments.filter(
            (comment) => comment.parent == 'null' && comment.score < 0
          )
          break
        case 'positive':
          this.comments = this.selectedStore.comments.filter(
            (comment) => comment.parent == 'null' && comment.score > 0
          )
          break
        default:
          this.comments = this.selectedStore.comments.filter(
            (comment) => comment.parent == 'null'
          )
          break
      }
      this.comments.sort((a, b) => {
        if (Math.abs(b.score) !== Math.abs(a.score)) {
          return Math.abs(b.score) - Math.abs(a.score)
        }
        return b.timestamp - a.timestamp
      })
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.card-slide-item {
  width: 300px;
}
.store-link {
  &:hover {
    text-decoration: underline;
  }
}
.link-icon {
  text-decoration: none;
}
.store-image-link {
  text-decoration: none;
}

.tag-icon {
  cursor: pointer;
  &:hover {
    filter: brightness(120%);
  }
}
.external-image {
  width: 24px;
  height: 24px;
  margin: 10px 10px 10px 25px;
}
.external-text {
  line-height: 44px;
}
@media only screen and (min-width: 600px) {
  .external-title {
    margin-top: 200px !important;
  }
}
</style>
