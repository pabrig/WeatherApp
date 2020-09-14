<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input @keyup.enter="getWeatherBySearch" rounded standout bottom-slots v-model="search" placeholder="Search city" dark >
        <template v-slot:append>
          <q-btn @click="getWeatherBySearch" title="Search" dense flat icon="search" /> 
          <q-btn @click="getLocation" title="My Location" name="my_location" dense flat icon="my_location" />         
        </template>

      </q-input>
    </div>

    <template v-if="WeatherData">
         <div class="col text-center q-mt-sm">
      <img :src="`http://openweathermap.org/img/wn/${WeatherData.weather[0].icon}@2x.png`" alt="">
    </div>
        <div class="col text-white text-center">
      <div class="text-h4 text-weight-light">
        {{ WeatherData.name }}
      </div>
      <div class="text-h6 text-weight-bold">
        {{WeatherData.weather[0].main}}
      </div>
      <div class="text-h2 q-mt-lg text-weight-thin text-center">
     {{Math.round(WeatherData.main.temp)}}   &deg
      
      </div>
    </div>

 
    </template>

    <template v-else>
      <div class="col column text-center  text-white">
        <div class="col text-h2 text-weight-thin">
           Weather App
        </div>
      </div>
      <q-btn  class="q-mb-auto " @click="getLocation"  rounded color="primary" size="1rem">
         <q-icon name="my_location" />
         <div class="q-pr-xs ">Find My Location</div>

      </q-btn>

    </template>

  
   
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data(){
    return{
      search: '',
      WeatherData: null,
      latitude: null,
      longtude: null,
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
      apiKey: 'ddeba87cc4db7bde795f8ed26eb06b7a',

    }
  },
  computed : {
    bgClass(){
      if(this.WeatherData){
        if(this.WeatherData.weather[0].icon.endsWith('n')){
          return 'bg-night'
        }
        else {
          return 'bg-day'
        }
      }
    }
  },
  methods: {

    getLocation (){
        this.$q.loading.show();

        if( this.$q.platform.is.electron){
          this.$axios.get('https://freegeoip.app/json/').then(response=>{
            this.latitude = repsonse.data.latitude
            this.longtude = repsonse.data.longitude
            this.getWeatherCoords()

          })

        }else{
           navigator.geolocation.getCurrentPosition(
        position => {
          console.log('position: ', position)
          this.latitude = position.coords.latitude;
          this.longtude = position.coords.longitude;
          this.getWeatherCoords();

        })

        }
     
    },
      getWeatherCoords(){
              this.$q.loading.show();
         this.$axios(`${this.apiUrl}?lat=${this.latitude}&lon=${this.longtude}&appid=${this.apiKey}&units=metric`)
          .then(response => {
            this.WeatherData = response.data
                   this.$q.loading.hide();
                   })
        },
    getWeatherBySearch (){
             this.$q.loading.show();

        navigator.geolocation.getCurrentPosition(
        position => {
          console.log('position: ', position)
          this.latitude = position.coords.latitude;
          this.longtude = position.coords.longitude;
          this.getWeatherCoords(
          this.$axios(`${this.apiUrl}?q=${this.search}&appid=${this.apiKey}&units=metric`)
          .then(response => {
            this.WeatherData = response.data
                   this.$q.loading.hide();})
          );

        })
        console.log('  getWeatherBySearch ')

    }
  }
}
</script>

<style lang="sass">
.q-page {
    background: linear-gradient(to top, #0575e6, #021b79)
  }
.bg-day{
  background: linear-gradient(to top, #56ccf2, #2f80ed)
  }
.bg-night{
   background: linear-gradient(to bottom, #141e30, #243b55)
  }
</style>



  