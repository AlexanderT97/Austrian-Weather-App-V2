<script setup>
import { ref, computed  } from 'vue';

const apiKey = import.meta.env.VITE_API_KEY;
const weatherAPI = "https://api.openweathermap.org/data/2.5/weather?";
const geoAPI = "https://api.openweathermap.org/geo/1.0/direct?";

const search = ref('');
const city = ref('Austrian');
const weatherIcon = ref('/icons/Austria_Bundesadler.svg');
const text = ref('I bi da Sepp eicha neia Weddaexperte <br> Gib oben den nom vo deim Kaff ei don sog i da’s Wedda bei dia');
const temp = ref('Weather App');

const isGif = computed(() => weatherIcon.value.endsWith('.gif'));

async function checkWeather(cityName) {
  try {
    if (!cityName.trim()) {
      throw new Error('Bitte gib einen Stadtnamen ein.');
    }

    const geoResponse = await fetch(`${geoAPI}q=${cityName}&limit=5&appid=${apiKey}`);
    const geoData = await geoResponse.json();

    if (!geoData.length) {
      throw new Error('Stadt nicht gefunden.');
    }

    const austrianCity = geoData.find(city => city.country === "AT");

    if (!austrianCity) {
      city.value = "";
      weatherIcon.value = "/icons/angry_arnold.gif"; 
      temp.value = "";
      text.value = "Des is ned in Österreich!";
      return;
    }

    const response = await fetch(`${weatherAPI}lat=${austrianCity.lat}&lon=${austrianCity.lon}&appid=${apiKey}&units=metric&lang=de`);
    const data = await response.json();

    city.value = data.name;
    temp.value = Math.round(data.main.temp) + " °C";
    weatherIcon.value = getWeatherIcon(data.weather[0].main);
    text.value = getWeatherText(data.weather[0].main);

  } catch (error) {
    console.error("Fehler:", error);
    city.value = "";
    weatherIcon.value = "/icons/stupid_arnold.gif";
    temp.value = "";
    text.value = "ÖHA! <br> de Stodt gibts ned <br> do host an Tippfehler!";
  }
}

function getWeatherIcon(weather) {
  const icons = {
    "Clouds": "/icons/cloudy.svg",
    "Clear": "/icons/clear_day.svg",
    "Thunderstorm": "/icons/thunderstorms.svg",
    "Drizzle": "/icons/drizzle.svg",
    "Snow": "/icons/snow.svg",
    "Rain": "/icons/rain.svg",
    "Mist": "/icons/mist.svg",
    "Haze": "/icons/haze.svg",
    "Dust": "/icons/dust.svg",
    "Fog": "/icons/fog.svg",
    "Tornado": "/icons/tornado.svg"
  };
  return icons[weather] || "/icons/default.svg";
}

function getWeatherText(weather) {
  const texts = {
    "Clouds": "Der Himmel schaut aus wia a grantiger Wiener – grau und unfreundlich.",
    "Clear": "Endlich amol a Wetter, wo ka Mensch sudern kann – owa a paar werdn’s trotzdem schaffen.",
    "Thunderstorm": "Wenn i des Geräusch hör, kriag i entweder Angst oder Hunger – es könnt a Magenknurren sein.",
    "Drizzle": "A so a Wetter, wo ma sich denkt: Hätt i an Schirm mitnehmen solln? Und wenn ja, wo is er jetzt?",
    "Snow": "Juhu, weiße Pracht! Und in 10 Minuten a gatschige Sauerei.",
    "Rain": "Es gibt ka schlechtes Wetter, nur schlechte Kleidung. Und i hob beides.",
    "Mist": "Wenn i jetz durchgeh, kumm i in a Paralleluniversum raus, oder?",
    "Haze": "A bisserl verschwommen, aber Hauptsach, i bin noch da.",
    "Dust": "I glab, i bin grad unabsichtlich Teil von an Westernfilm worden.",
    "Fog": "So a Nebel, dass ma fast vergisst, dass ma scho wach is.",
    "Tornado": "Wenn’s so weida geht, brauch i bald an Sicherheitsgurt für dahoam."
  };
  return texts[weather] || "Unbekanntes Wetter!";
}


function updateRealVH() {
  document.documentElement.style.setProperty('--real-vh', `${window.innerHeight}px`);
}

window.addEventListener('resize', updateRealVH);
updateRealVH();



</script>

<template>

<div class="weather">

<div class="input">

    <div class="city-input">
      <input v-model="search" type="text" spellcheck="false">
      <img src="/icons/search.svg" id="search" @click="checkWeather(search)" >
    </div> 
    <h1 class="city">{{ city }}</h1>
    <img :src="weatherIcon" alt="Wetter-Icon" class="weather-icon" :class="{ 'arni': isGif }"/>
    <p class="temp">{{ temp }}</p>
</div>

    <div class="sepp_box">
        <div class="bubble bubble_bottom">
            <p class="text" v-html="text"></p>
        </div>
        
        <img src="/sepp.webp" alt="I bi da Sepp" id="sepp"/>
    </div>
    
    


</div>

</template>

