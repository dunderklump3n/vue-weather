<template>
 <div id="app">
  <main>
    <div class="search-box">
      <input 
      type="text" 
      class="search-bar" 
      placeholder="Search..."
      v-model="query"
      @keypress="fetchWeather"
      />
      
    </div>


    <div class="weather-wrap" v-if="typeof weather.main !='undefined'">
      <div class="location-box">
        <div class="location">{{weather.name}}, {{ weather.sys.country }}</div>
        <div class="date">{{dateBuilder()}}</div> 
      </div>

      <div class="weather-box">
          <div class="temp"> {{Math.round(weather.main.temp)}}°c</div>
          <div class="weather">{{weather.weather[0].main}}</div>
      </div>
    </div>


  </main>
  <div>
    <ul>
    <TreeItem class="item" :model="treeData"></TreeItem>
  </ul>
  </div>
 </div>
</template>

<script>
import TreeItem from './components/TreeItem.vue'

const treeData = {
  name: 'My Tree',
  children: [
    { name: 'hello' },
    { name: 'wat' },
    {
      name: 'child folder',
      children: [
        {
          name: 'child folder',
          children: [{ name: 'hello' }, { name: 'wat' }]
        },
        { name: 'hello' },
        { name: 'wat' },
        {
          name: 'child folder',
          children: [{ name: 'hello' }, { name: 'wat' }]
        }
      ]
    }
  ]
}

export default {
  name: 'App',
  data() {
    return {
      api_key: 'ec222c7551bfa6b2ca54384fa0358c2b',
      url_base: 'https://api.openweathermap.org/data/2.5',
      url_base2: 'http://api.openweathermap.org/geo/1.0',
      query: '',
      weather: {},
      treeData: treeData // Add treeData to the data section
    }
  },
  methods: {
    async fetchWeather(e) {
      if (e.key === "Enter") {
        try {
          // First API call to get location data
          const locationResponse = await fetch(
            `${this.url_base2}/direct?q=${this.query}&limit=1&appid=${this.api_key}`
          );
          const locationData = await locationResponse.json();

          if (locationData.length === 0) {
            // Handle the case where location data is not found
            console.error("Location not found");
            return;
          }

          const latitude = locationData[0].lat;
          const longitude = locationData[0].lon;

          // Second API call using location data
          const weatherResponse = await fetch(
            `${this.url_base}/weather?lat=${latitude}&lon=${longitude}&units=metric&APPID=${this.api_key}`
          );
          const weatherData = await weatherResponse.json();

          this.weather = weatherData;
        } catch (error) {
          console.error("Error fetching data:", error);
        }
      }
    },
    setResults(results) {
      this.weather = results;
    },
    dateBuilder() {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    }
  },
  components: {
    TreeItem
  }
}
</script>


<style>
{
  margin: 0;
  padding: 0;
  box-sizing:border-box;
}

body{
  font-family: 'montserrat', sans-serif;

}

#app{
  background-image: url('./assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;

}

main{
  min-height: 100vh;
  padding: 25px;
background-image: linear-gradient(to bottom, rgba(0,0,0,0.25), rgba(0,0,0,0.75));
}

.search-box{
  width: 85%;
  margin-bottom: 30px;
  padding: 30px;

}

.search-box .search-bar{
display: block;
width: 100%;
padding: 15px;

color: #313131;
font-size:20px;

appearance: none;
border:none;
outline:none;
background:none;

box-shadow: 0px 0px 8px rgba(0,0,0,0.25);
background-color: rgba(255,255,255, 0.5);
border-radius: 0px 16px 0px 16px;
transition: 0.4s;
}

.search-box .search-bar:focus{
  box-shadow: 0px 0px 16px rgba(0,0,0,0.25);
  background-color: rgba(255,255,255, 0.75);
  border-radius: 16px 0px 16px 0px;
}


.location-box .location{
  color:#FFF;
  font-size:32px;
  font-weight:600;
  text-align: center;
  text-shadow: 1px 3px rgba(0,0,0,0.25);

}

.location-box .date{
  color:#FFF;
  font-size: 20px;
  font-weight: 500;
  text-align: center;
  font-style:italic;

}

.weather-box{
  text-align: center;
}

.weather-box .temp{
  display: inline-block;
  padding: 10px 25px;
  color:#FFF;
  font-size:102px;
  font-weight: 900;

  text-shadow:3px 6px rgba(0,0,0,0.25);
  background-color:rgba(255,255,255,0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);

}

.weather-box .weather{
  color:#FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0,0,0,0.25)
}


</style>
