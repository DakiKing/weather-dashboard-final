<template>
  <div class="container">
    <h1>Weather Dashboard</h1>
    <SearchBar @search="fetchWeather"/>
    <Favorites :favorites="favorites" @select="fetchWeather"/>
    <WeatherDisplay 
      v-if="currentWeather" 
      :weather="currentWeather" 
      :favorites="favorites" 
      @toggle-favorite="toggleFavorite"
    />
    <Forecast v-if="forecast" :forecast="forecast"/>
    <FiveDayForecast v-if="forecast" :forecastData="forecast"/>

    
    <div v-if="listening" class="listening-indicator">Listening...</div>
  </div>
</template>

<script>
import SearchBar from './components/SearchBar.vue';
import WeatherDisplay from './components/WeatherDisplay.vue';
import Forecast from './components/Forecast.vue';
import Favorites from './components/Favorites.vue';
import FiveDayForecast from './components/FiveDayForecast.vue';

export default {
  components: {
    SearchBar,
    WeatherDisplay,
    Forecast,
    Favorites,
    FiveDayForecast,
  },
  data() {
    return {
      currentWeather: null,
      forecast: null,
      favorites: JSON.parse(localStorage.getItem('favoriteLocations')) || [],
      currentLocation: null, 
      recognition: null,     
      listening: false,      
    };
  },
  created() {
    
    if (!localStorage.getItem('favoriteLocations')) {
      localStorage.setItem('favoriteLocations', JSON.stringify([]));
    }
    
    
    this.getCurrentLocation();

    
    this.setupSpeechRecognition();

    
    window.addEventListener('keydown', this.handleKeyDown);
    window.addEventListener('keyup', this.handleKeyUp);
  },
  beforeDestroy() {
    window.removeEventListener('keydown', this.handleKeyDown);
    window.removeEventListener('keyup', this.handleKeyUp);
  },
  methods: {
    fetchWeather(location) {
      const apiKey = '58e9cbe09caef5824249382dc08ff1e1';
      const apiBaseUrl = 'https://api.openweathermap.org/data/2.5/';

      fetch(`${apiBaseUrl}weather?q=${location}&appid=${apiKey}&units=metric`)
        .then(response => response.json())
        .then(data => {
          if (data.cod === 200) {
            this.currentWeather = data;
            this.fetchForecast(location);
          } else {
            alert('Location not found');
          }
        })
        .catch(error => console.error('Error fetching weather data:', error));
    },
    fetchForecast(location) {
      const apiKey = '58e9cbe09caef5824249382dc08ff1e1';
      const apiBaseUrl = 'https://api.openweathermap.org/data/2.5/';

      fetch(`${apiBaseUrl}forecast?q=${location}&appid=${apiKey}&units=metric`)
        .then(response => response.json())
        .then(data => {
          this.forecast = data;
        })
        .catch(error => console.error('Error fetching forecast data:', error));
    },
    toggleFavorite(location) {
      let favorites = [...this.favorites];

      if (favorites.includes(location)) {
        if (location === this.currentLocation) {
          console.log("Cannot remove current location from favorites.");
          return;  
        }
        favorites = favorites.filter(fav => fav !== location);
      } else {
        favorites.push(location);
      }

      this.favorites = favorites;
      localStorage.setItem('favoriteLocations', JSON.stringify(favorites));
    },
    
    getCurrentLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            const lat = position.coords.latitude;
            const lon = position.coords.longitude;
            console.log(`Latitude: ${lat}, Longitude: ${lon}`);  

            this.fetchWeatherByCoords(lat, lon);
          },
          (error) => {
            console.error('Geolocation error:', error);
            alert('Unable to retrieve your location. Please search manually.');
          }
        );
      } else {
        console.error('Geolocation is not supported by this browser.');
        alert('Geolocation is not supported by your browser.');
      }
    },
    
    fetchWeatherByCoords(lat, lon) {
      const apiKey = '58e9cbe09caef5824249382dc08ff1e1';
      const apiBaseUrl = 'https://api.openweathermap.org/data/2.5/';

      const url = `${apiBaseUrl}weather?lat=${lat}&lon=${lon}&appid=${apiKey}&units=metric`;
      console.log(`Fetching weather data from: ${url}`);  

      fetch(url)
        .then((response) => response.json())
        .then((data) => {
          console.log('Weather data for current location:', data);  

          if (data.cod === 200) {
            this.currentLocation = data.name;  
            this.currentWeather = data;
            this.addCurrentLocationToFavorites();
            this.fetchForecast(data.name);
          } else {
            console.error('Error fetching weather for current location:', data);
          }
        })
        .catch((error) => console.error('Error fetching weather data by coordinates:', error));
    },
    
    addCurrentLocationToFavorites() {
      if (!this.favorites.includes(this.currentLocation)) {
        this.favorites.push(this.currentLocation);
        localStorage.setItem('favoriteLocations', JSON.stringify(this.favorites));
      }
    },
    
    setupSpeechRecognition() {
      const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
      this.recognition = new SpeechRecognition();
      this.recognition.continuous = false;
      this.recognition.lang = 'en-US';

      this.recognition.onresult = (event) => {
        const transcript = event.results[0][0].transcript.toLowerCase().trim();
        const confidence = event.results[0][0].confidence;

        
        console.log(`Transcript: "${transcript}"`);
        console.log(`Confidence level: ${confidence}`);

        
        if (confidence > 0.75) {
          this.processVoiceCommand(transcript);
        } else {
          console.log("Low confidence in recognition, please try again.");
        }
      };

      this.recognition.onstart = () => {
        this.listening = true;
      };

      this.recognition.onend = () => {
        this.listening = false;
      };
    },
    
    processVoiceCommand(command) {
      
      const words = command.split(" ");
      const action = words[0];  
      const city = words.slice(1).join(" ");  

      if (action === "search") {
        this.fetchWeather(city);
      } else {
        console.log("Command not recognized.");
      }
    },
    
    handleKeyDown(event) {
      if (event.key === 'v' && !this.listening) {
        this.recognition.start();  
      }
    },
    
    handleKeyUp(event) {
      if (event.key === 'v' && this.listening) {
        this.recognition.stop();  
      }
    }
  }
};
</script>

<style>
.listening-indicator {
  background-color: rgba(255, 0, 0, 0.5);
  color: white;
  padding: 10px;
  position: fixed;
  bottom: 10px;
  right: 10px;
  border-radius: 5px;
  animation: pulse 1s infinite;
  display: flex;
  align-items: center;
  justify-content: center;
}

.mic-icon {
  font-size: 24px;
  margin-right: 10px;
}

@keyframes pulse {
  0% {
    transform: scale(1);
    opacity: 0.8;
  }
  50% {
    transform: scale(1.1);
    opacity: 1;
  }
  100% {
    transform: scale(1);
    opacity: 0.8;
  }
}
</style>
