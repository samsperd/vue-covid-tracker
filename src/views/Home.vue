<template>
  <main class="mb-5" v-if="!loading" >
    <div class="grid grid-cols-2">
      <div class="basics-1/2">
        <h6 class="font-bold">Track your country's covid data</h6>
      </div>
      <div class="basics-1/2 relative">
        <button @click="clearCountryData" v-if="stats.Country" class="bg-gray-200 rounded p-1 px-3 absolute inset-y-0 right-0">
          Reset
        </button>

      </div>
    </div>    

    <CountrySelect @get-country="getCountryData" :countries="countries" ></CountrySelect>

    <DataTitle :text="title" :dataDate="dataDate" />

    <DataBoxes :stats="stats" />

  </main>

  <main class="flex flex-col align-center justify-center text-center" v-else>
    <div class="text-gray-500 text-3l mt-10 mb-6">
      Fertching Data
    </div>
    <img :src="loadingImage" class="w-24 m-auto" alt="" >
  </main>

</template>

<script>
import DataTitle from '@/components/DataTitle'
import DataBoxes from '@/components/DataBoxes'
import CountrySelect from '@/components/CountrySelect'

export default {
  name: 'Home',
  components: {
    DataTitle,
    DataBoxes,
    CountrySelect,
  },
  data() {
    return {
      loading: true,
      title: 'Global',
      dataDate: '',
      stats: {},
      countries: [],
      loadingImage: require('../assets/hourglass.gif')
    }
  },
  methods: {
    async fetchCovidData() {
      const res = await fetch('https://api.covid19api.com/summary')
      const data = await res.json()
      return data
    },
    getCountryData(country) {
      this.stats = country
      this.title = country.Country
    },
    async clearCountryData() {
      this.loading = true
      const data = await this.fetchCovidData()
      this.title = 'Global'
      this.stats = data.Global
      this.loading = false
    }
  },
  async created() {
    const data = await this.fetchCovidData()
    
    this.dataDate = data.Date
    this.stats = data.Global
    this.countries = data.Countries
    this.loading = false
  }
}
</script>
