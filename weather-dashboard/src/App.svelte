<script>

import { onMount } from "svelte";

let city = 'London';
let weatherData = null;
let futureWeatherData = null;
let error = null;

// Your WeatherAPI key
const apiKey = '9f80ff670a774d2989e12029242509';

const getUserLocation = () => {
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(success, handleLocationError);
    } else {
      error = "Geolocation is not supported by this browser.";
    }
  };

  // Handle successful retrieval of the user's position
  const success = (position) => {
    const { latitude, longitude } = position.coords;
    city = `${latitude},${longitude}`;
    fetchWeather();
    fetchForecastedWeather()
  };

  // Handle error in retrieving location
  const handleLocationError = () => {
    console.log("Unable to retrieve your location. Defaulting to London.");
    fetchWeather(); // Fetch default city's weather if geolocation fails
    fetchForecastedWeather()
  };


// Fetch weather data from WeatherAPI
const fetchWeather = async () => {
  try {
    const res = await fetch(
      `http://api.weatherapi.com/v1/current.json?key=${apiKey}&q=${city}&aqi=no`
    );
    if (!res.ok) {
      throw new Error('Could not fetch weather data');
    }
    weatherData = await res.json();
  } catch (err) {
    error = err.message;
  }
};

const fetchForecastedWeather = async () => {
    try {
      const res = await fetch(
        `http://api.weatherapi.com/v1/forecast.json?key=${apiKey}&q=${city}&days=5&aqi=no&alerts=no`
      );
      if (!res.ok) {
        throw new Error('Could not fetch weather data');
      }
      futureWeatherData = await res.json();
    } catch (err) {
      error = err.message;
    }
  };

// Fetch weather when the component is first loaded
onMount(() => {
  getUserLocation();
});

</script>

<style>
main {
  background: linear-gradient(to right, #6c8cb4, #a1b2bb); 
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}


.lds-ring,
.lds-ring div {
  box-sizing: border-box;
}
.lds-ring {
  display: inline-block;
  position: relative;
  width: 160px;
  height: 160px;
  color: rgb(65, 65, 110);
  
}
.lds-ring div {
  box-sizing: border-box;
  display: block;
  position: absolute;
  width: 64px;
  height: 64px;
  margin: 8px;
  border: 8px solid currentColor;
  border-radius: 50%;
  animation: lds-ring 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
  border-color: currentColor transparent transparent transparent;
}
.lds-ring div:nth-child(1) {
  animation-delay: -0.45s;
}
.lds-ring div:nth-child(2) {
  animation-delay: -0.3s;
}
.lds-ring div:nth-child(3) {
  animation-delay: -0.15s;
}
@keyframes lds-ring {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

#title{
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.3);
  border-radius: 10px;
  color: white;
  width: 90vw;
  height: 10vh;
  font-size: 2vw;
}

</style>

<main>

 
  <div id="site-container">
    {#if futureWeatherData}
      <div id="weather-container">
        <h2 id="title">{futureWeatherData.location.name}, {futureWeatherData.location.country}</h2>

          <div id="weather-display">
            {#each futureWeatherData.forecast.forecastday as day}
              <div class="individual-day-display">
                <p>{day.date}</p>
                <p>Max Temp: {day.day.maxtemp_c}°C</p>
                <p>Min Temp: {day.day.mintemp_c}°C</p>
                <p>Condition: {day.day.condition.text}</p>
                <img src={day.day.condition.icon} alt="Weather Icon" />
              </div>
            {/each}
        </div>
      </div>
    {:else}
      <div class="lds-ring"><div></div><div></div><div></div><div></div></div>  

    {/if}

  </div>
  

  <!-- {#if error}
    <p class="error">{error}</p>
  {/if}

  {#if weatherData}
    <h2>Weather in {weatherData.location.name}, {weatherData.location.country}</h2>
    <p>Temperature: {weatherData.current.temp_c}°C</p>
    <p>Condition: {weatherData.current.condition.text}</p>
    <img src={weatherData.current.condition.icon} alt="Weather Icon" />
  {/if}

  {#if futureWeatherData}
    <h3>Upcoming Weather:</h3>
    {#each futureWeatherData.forecast.forecastday as day}
      <div>
        <p>{day.date}</p>
        <p>Max Temp: {day.day.maxtemp_c}°C</p>
        <p>Min Temp: {day.day.mintemp_c}°C</p>
        <p>Condition: {day.day.condition.text}</p>
        <img src={day.day.condition.icon} alt="Weather Icon" />
      </div>
    {/each}
  {/if} -->

</main>