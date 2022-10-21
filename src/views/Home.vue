<template>
  <div class="my-5">
    <v-row justify="center">
      <v-col cols="6">
        <v-text-field
          placeholder="Search collection (Enter Address)"
          v-model="search"
          outlined
          dense
        ></v-text-field>
      </v-col>
      <v-col cols="3">
        <v-btn depressed @click="searchCollection">Search Collection</v-btn>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <h4 class="ml-5">Search Results:</h4>
        <v-card
          class="bg-green ma-5"
          width="25vw"
          elevation="0"
          :to="{
            name: 'NFT',
            params: { count: searchItem.count, address: searchItem.address }
          }"
        >
          <v-card-text>
            <p>Title: {{ searchItem.name }}</p>
            <p>Description: {{ searchItem.short_description }}</p>
            <p>Address: {{ searchItem.address }}</p>
            <p>Number of NFTs: {{ searchItem.count }}</p>
            <p>Owners: {{ searchItem.num_owners }}</p>
            <div class="text--primary">
              <v-img :src="searchItem.banner_image_url"></v-img>
            </div>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-row>
      <v-col v-for="item in collections" :key="item.name">
        <v-card
          class="bg-green ma-5"
          width="25vw"
          elevation="0"
          :to="{
            name: 'NFT',
            params: { count: item.count, address: item.address }
          }"
        >
          <v-card-text>
            <p>Title: {{ item.name }}</p>
            <p>Description: {{ item.short_description }}</p>
            <p>Address: {{ item.address }}</p>
            <p>Number of NFTs: {{ item.count }}</p>
            <p>Owners: {{ item.num_owners }}</p>
            <div class="text--primary">
              <v-img :src="item.banner_image_url"></v-img>
            </div>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-snackbar v-model="snackbar">
      {{ text }}

      <template v-slot:action="{ attrs }">
        <v-btn color="pink" text v-bind="attrs" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'

export default {
  name: 'Home',
  data: () => ({
    collections: [],
    search: '',
    snackbar: false,
    text: '',
    searchItem: {}
  }),
  components: {},
  methods: {
    async searchCollection () {
      const timer = ms => new Promise(res => setTimeout(res, ms))

      const res = await axios
        .get(
          `https://testnets-api.opensea.io/api/v1/asset_contract/${this.search}`
        )
        .catch(() => {
          this.snackbar = true
          this.text = "Invalid Collection Address (Couldn't fetch details)"
          this.search = ''
        })
      await timer(1000)

      const slugRes = await axios
        .get(
          `https://testnets-api.opensea.io/api/v1/collection/${res.data.collection.slug}`
        )
        .catch(() => {
          this.snackbar = true
          this.text = 'Something Went Wrong..!'
        })
      let details = {
        banner_image_url: slugRes.data.collection.image_url,
        name: slugRes.data.collection.name,
        short_description: slugRes.data.collection.short_description,
        address: slugRes.data.collection.primary_asset_contracts[0].address,
        count: slugRes.data.collection.stats.count,
        num_owners: slugRes.data.collection.stats.num_owners
      }
      this.searchItem = details
    }
  },
  async mounted () {
    const timer = ms => new Promise(res => setTimeout(res, ms))
    this.slugs = [
      'lu-demo',
      'test-urban-miner-project-nft-v2',
      'nft-cloner-9000'
    ]
    for (let index = 0; index < this.slugs.length; index++) {
      const res = await axios
        .get(
          `https://testnets-api.opensea.io/api/v1/collection/${this.slugs[index]}`
        )
        .catch(() => {
          this.snackbar = true
          this.text = 'Something Went Wrong..!'
        })
      let details = {
        banner_image_url: res.data.collection.image_url,
        name: res.data.collection.name,
        short_description: res.data.collection.short_description,
        address: res.data.collection.primary_asset_contracts[0].address,
        count: res.data.collection.stats.count,
        num_owners: res.data.collection.stats.num_owners
      }
      this.collections.push(details)
      await timer(1000)
    }
  }
}
</script>
