<template>
  <v-row>
    <v-col v-for="item in collections" :key="item.name">
      <v-card class="bg-green ma-5" width="25vw" elevation="0">
        <v-card-text>
          <p>NFT-Id: {{ item.id }}</p>
          <p>Collection Addres: {{ item.address }}</p>
          <div class="text--primary">
            <v-img :src="item.banner_image_url"></v-img>
          </div>
        </v-card-text>
        <v-card-actions class="justify-center">
        <v-btn depressed small :href="item.opensea_url" target="_blank"> View on Opensea</v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'

export default {
  name: 'NFT',
  data: () => ({
    collections: []
  }),
  components: {},
  async mounted () {
    const timer = ms => new Promise(res => setTimeout(res, ms))
    const address = this.$route.params.address
    const count = this.$route.params.count
    if (address && count) {
      for (let index = 1; index < count; index++) {
        const response = await axios.get(
          `https://testnets-api.opensea.io/api/v1/asset/${address}/${index}/`
        ).catch(()=> {})
        this.collections.push({
          banner_image_url: response.data.image_url,
          id: index,
          address: address,
          opensea_url: response.data.permalink
        })
        await timer(1200)
      }
    }
  }
}
</script>
