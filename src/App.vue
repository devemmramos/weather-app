<template>
  <div :class="[isDarkMode ? 'bg-slate-900 dark' : 'bg-blue-100', 'min-h-screen', 'flex', 'flex-col', 'items-center', 'justify-center', 'transition-colors', 'duration-500', 'px-4', 'py-6']">
    <!-- Dark Mode Toggle Icon -->
   <button
      @click="toggleDarkMode"
      class="absolute top-4 right-4 p-2 rounded-full focus:outline-none bg-gray-200 dark:bg-gray-800 hover:bg-gray-300 dark:hover:bg-gray-700 transition-colors"
    >
    <svg v-if="isDarkMode" viewBox="0 0 24 24" fill="none" class="w-6 h-6"><path fill-rule="evenodd" clip-rule="evenodd" d="M17.715 15.15A6.5 6.5 0 0 1 9 6.035C6.106 6.922 4 9.645 4 12.867c0 3.94 3.153 7.136 7.042 7.136 3.101 0 5.734-2.032 6.673-4.853Z" class="fill-sky-400/20"></path><path d="m17.715 15.15.95.316a1 1 0 0 0-1.445-1.185l.495.869ZM9 6.035l.846.534a1 1 0 0 0-1.14-1.49L9 6.035Zm8.221 8.246a5.47 5.47 0 0 1-2.72.718v2a7.47 7.47 0 0 0 3.71-.98l-.99-1.738Zm-2.72.718A5.5 5.5 0 0 1 9 9.5H7a7.5 7.5 0 0 0 7.5 7.5v-2ZM9 9.5c0-1.079.31-2.082.845-2.93L8.153 5.5A7.47 7.47 0 0 0 7 9.5h2Zm-4 3.368C5 10.089 6.815 7.75 9.292 6.99L8.706 5.08C5.397 6.094 3 9.201 3 12.867h2Zm6.042 6.136C7.718 19.003 5 16.268 5 12.867H3c0 4.48 3.588 8.136 8.042 8.136v-2Zm5.725-4.17c-.81 2.433-3.074 4.17-5.725 4.17v2c3.552 0 6.553-2.327 7.622-5.537l-1.897-.632Z" class="fill-sky-500"></path><path fill-rule="evenodd" clip-rule="evenodd" d="M17 3a1 1 0 0 1 1 1 2 2 0 0 0 2 2 1 1 0 1 1 0 2 2 2 0 0 0-2 2 1 1 0 1 1-2 0 2 2 0 0 0-2-2 1 1 0 1 1 0-2 2 2 0 0 0 2-2 1 1 0 0 1 1-1Z" class="fill-sky-500"></path></svg>
      <svg v-else viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="w-6 h-6"><path d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z" class="fill-sky-400/20 stroke-sky-500"></path><path d="M12 4v1M17.66 6.344l-.828.828M20.005 12.004h-1M17.66 17.664l-.828-.828M12 20.01V19M6.34 17.664l.835-.836M3.995 12.004h1.01M6 6l.835.836" class="stroke-sky-500"></path></svg>
    </button>

    <h1 class="text-3xl sm:text-4xl font-bold dark:text-white text-gray-900 mb-6 duration-500 text-center">
      Weather Application
    </h1>

    <div class="justify-center weather-form flex flex-col sm:flex-row space-y-4 sm:space-y-0 sm:space-x-4 mb-6 w-full max-w-md mx-auto">
      <input
        type="text"
        v-model="city"
        @keydown.enter="fetchWeather"
        class="px-4 py-2 rounded-lg shadow-md border border-gray-300 text-slate-600 dark:border-gray-700 dark:bg-gray-800 dark:text-white focus:outline-none focus:ring-2 focus:ring-blue-500 w-full sm:w-auto"
        placeholder="Enter city name"
      />
      <button
        @click="fetchWeather"
        class="bg-blue-500 text-white px-4 py-2 rounded-lg hover:bg-blue-600 focus:outline-none"
      >
       <svg width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="m19 19-3.5-3.5"></path><circle cx="11" cy="11" r="6"></circle></svg>
      </button>
    </div>

    <!-- Weather and Forecast Container -->
    <div class="weather-forecast-container flex flex-col gap-6 lg:flex-row lg:space-x-6 w-full max-w-4xl">
      <!-- Current Weather Info -->
      <div v-if="weatherData" class="weather-info p-6 rounded-lg shadow-lg bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 w-full lg:w-1/3">
        <!-- City Name -->
        <h2 class="text-2xl sm:text-3xl font-semibold mb-2">{{ weatherData.name }}</h2>

        <!-- Weather Icon and Temperature -->
        <div class="flex items-center justify-center mb-4">
          <img
            v-if="weatherIcon"
            :src="weatherIcon"
            alt="Weather icon"
            class="w-12 h-12 sm:w-16 sm:h-16"
          />
          <p class="text-4xl sm:text-5xl font-bold ml-4">{{ weatherData.main.temp }}°</p>
        </div>

        <!-- Weather Condition -->
        <p class="text-lg sm:text-xl capitalize">{{ weatherData.weather[0].description }}</p>

        <!-- Additional Weather Info -->
        <div class="mt-4 text-sm sm:text-base">
          <p>Humidity: {{ weatherData.main.humidity }}%</p>
          <p>Wind: {{ weatherData.wind.speed }} {{ unit === 'metric' ? 'm/s' : 'mph' }}</p>
        </div>
      </div>

      <!-- 5-Day Forecast -->
      <div v-if="forecastData" class="forecast-info p-6 rounded-lg shadow-lg bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 w-full lg:w-2/3 overflow-x-auto">
        <h2 class="text-xl sm:text-2xl font-semibold mb-4">5-Day Forecast</h2>
        <div class="w-full">
          <table class="min-w-full divide-y">
            <thead class="bg-gray-50 dark:bg-gray-800">
              <tr>
                <th class="px-2 py-1 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Date</th>
                <th class="px-2 py-1 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Weather</th>
                <th class="px-2 py-1 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Temperature</th>
                <th class="px-2 py-1 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Description</th>
              </tr>
            </thead>
            <tbody class="bg-white dark:bg-gray-800 divide-y">
              <tr v-for="(day, index) in forecastData.list.filter((_, i) => i % 8 === 0).slice(0, 5)" :key="index">
                <td class="px-2 py-1 text-sm font-medium text-gray-900 dark:text-gray-100">
                  {{ formatDate(day.dt_txt) }}
                </td>
                <td class="px-2 py-1 text-sm text-gray-500 dark:text-gray-300">
                  <img
                    :src="'http://openweathermap.org/img/wn/' + day.weather[0].icon + '@2x.png'"
                    alt="Weather icon"
                    class="w-6 h-6 inline"
                  />
                </td>
                <td class="px-2 py-1 text-sm text-gray-500 dark:text-gray-300">
                  {{ day.main.temp }}°
                </td>
                <td class="px-2 py-1 text-sm text-gray-500 dark:text-gray-300">
                  {{ day.weather[0].description }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- Error Message -->
    <div v-if="errorMessage" class="error mt-4 text-red-500 dark:text-red-400">
      <p>{{ errorMessage }}</p>
    </div>

    <!-- Unit Toggle -->
    <div class="unit-toggle mt-6">
      <button
        @click="toggleUnit"
        class="text-blue-500 dark:text-blue-300 underline hover:text-blue-700 dark:hover:text-blue-400 focus:outline-none"
      >
        Switch to {{ unit === 'metric' ? 'Imperial (Fahrenheit)' : 'Metric (Celsius)' }}
      </button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      city: '',
      weatherData: null,
      forecastData: null,
      errorMessage: '',
      unit: 'metric', // Default to metric (Celsius)
      apiKey: '46c710c3c7184119448daf16742244b6',
      isDarkMode: false, // Local state to manage dark mode
      weatherIcon: '', // Store the weather icon URL
    };
  },
  methods: {
    async fetchWeather() {
      this.errorMessage = '';
      try {
        // Fetch current weather
        const weatherResponse = await axios.get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=${this.unit}&appid=${this.apiKey}`
        );
        this.weatherData = weatherResponse.data;
        this.updateWeatherIcon();

        // Fetch 5-day forecast
        const forecastResponse = await axios.get(
          `https://api.openweathermap.org/data/2.5/forecast?q=${this.city}&units=${this.unit}&appid=${this.apiKey}`
        );
        this.forecastData = forecastResponse.data;
      } catch (error) {
        console.error(error);
        this.weatherData = null;
        this.forecastData = null;
        this.errorMessage = 'An error occurred. Please try again later.';
      }
    },
    updateWeatherIcon() {
      if (this.weatherData && this.weatherData.weather.length > 0) {
        this.weatherIcon = `http://openweathermap.org/img/wn/${this.weatherData.weather[0].icon}@2x.png`;
      }
    },
    toggleUnit() {
      this.unit = this.unit === 'metric' ? 'imperial' : 'metric';
      this.fetchWeather(); 
    },
    toggleDarkMode() {
      this.isDarkMode = !this.isDarkMode;
    },
    formatDate(dateString) {
      const date = new Date(dateString);
      const options = { weekday: 'short', month: 'short', day: 'numeric' };
      return date.toLocaleDateString(undefined, options);
    }
  }
};
</script>

<style scoped>
.weather-form {
  max-width: 100%;
}

.weather-info {
  max-width: 100%;
}

.forecast-info {
  max-width: 100%;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  padding: 0.5rem;
  text-align: left;
}


@media (min-width: 640px) {
  .weather-form {
    flex-direction: row;
  }
}

@media (min-width: 1024px) {
  .weather-forecast-container {
    flex-direction: row;
  }
}

@media (max-width: 640px) {
  .forecast-info {
    overflow-x: auto;
  }
}
</style>
