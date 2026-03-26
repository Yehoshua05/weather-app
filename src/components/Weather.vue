<!-- <template>
    <div class="container p-0">
        <div class="d-flex">
            <div class="card main-div w-100">
                <div class="p-3">
                    <h2 class="mb-1 day">Tuesday</h2>
                    <p class="text-light date mb-0">{{date}}</p>
                    <small>{{time}}</small>
                    <h2 class="place"><i class="fa fa-location">{{ name }} <small>{{ country }}</small></i></h2>
                    <div class="temp">
                        <h1 class="weather-temp">{{ temperature }}&deg;</h1>
                        <h2 class="text-light">{{description}} <img :src="iconurl"></h2>
                    </div>
                </div>
            </div>
            <div class="card card-2 w-100">
                <table class="m-4">
                    <tbody>
                        <tr>
                            <th>Sea Level</th>
                            <td>100</td>
                        </tr>
                        <tr>
                            <th>Sea Level</th>
                            <td>100</td>
                        </tr>
                        <tr>
                            <th>Sea Level</th>
                            <td>100</td>
                        </tr>
                    </tbody>
                </table>
    
                <DaysWeather></DaysWeather>
    
                <div id="div_Form" class="d-flex m-3 justify-content-center">
                    <form action="">
                        <input type="button" value="Change Location" class="btn change-btn btn-primary">
                    </form>
                </div>
            </div>
        </div>
    </div>
</template> -->

<template>
    <div class="container p-0">
        <div class="d-flex">
            <div class="card main-div w-100" :class="weatherClass">
                <div class="p-3">
                    <h2 class="mb-1 day">Today</h2>
                    <p class="text-light date mb-0">{{ date }}</p>
                    <small>{{ time }}</small>
                    <h2 class="place">{{ name }} <small>{{ country }}</small></h2>
                    <div class="temp">
                        <h1 class="weather-temp">{{ temperature }}&deg;</h1>
                        <h2 class="text-light">
                            {{ description }} <img :src="iconurl" alt="weather icon">
                        </h2>
                    </div>
                </div>
            </div>
            <div class="card card-2 w-100">
                <table class="m-4">
                    <tbody>
                        <tr>
                            <th>Sea Level</th>
                            <td v-if="seaLevel > 0">{{ seaLevel }} hPa</td>
                            <td v-else>N/A</td>
                        </tr>
                        <tr>
                            <th>Humidity</th>
                            <td>{{ humidity }}%</td>
                        </tr>
                        <tr>
                            <th>Wind</th>
                            <td>{{ windSpeed }} m/s</td>
                        </tr>
                    </tbody>
                </table>

                <DaysWeather :cityname="cityname"></DaysWeather>

                <div id="div_Form" class="d-flex m-3 justify-content-center">
                    <form action="">
                        <input type="button" value="Change Location" @click="changelocation" class="btn change_btn btn-primary">
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>


<script>
import axios from 'axios';
import DaysWeather from './DaysWeather';

export default {
    name: 'myWeather',
    components: {
        DaysWeather,
    },
    props: {
        city: {
            type: String,
            required: true,
        },
    },
    data() {
        return {
            cityname: this.city,
            temperature: null,
            description: null,
            iconurl: null,
            date: null,
            time: null,
            name: null,
            country: null,
            seaLevel: null,
            humidity: null,
            windSpeed: null,
            newCity: '',
            isLoading: true,
            monthNames: ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"],
        };
    },
    async created() {
        await this.fetchWeatherData(this.city);
        this.updateDateTime();
    },
    methods: {
        async fetchWeatherData(city) {
            this.isLoading = true;
            try {
                const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&appid=94401aff94104727b56d78eb1d6f8f51`);
                const weatherData = response.data;
                this.temperature = Math.round(weatherData.main.temp);
                this.description = weatherData.weather[0].description;
                this.name = weatherData.name;
                this.country = weatherData.sys.country;
                this.seaLevel = weatherData.main.sea_level || 'N/A';
                this.humidity = weatherData.main.humidity;
                this.windSpeed = weatherData.wind.speed;
                this.iconurl = `https://openweathermap.org/img/w/${weatherData.weather[0].icon}.png`;
            } catch (error) {
                console.error('Error fetching weather data:', error);
            } finally {
                this.isLoading = false;
            }
        },
        updateDateTime() {
            const d = new Date();
            this.date = `${d.getDate()}-${this.monthNames[d.getMonth()]}-${d.getFullYear()}`;
            this.time = `${d.getHours()}:${d.getMinutes()}:${d.getSeconds()}`;
        },
        changelocation() {
            window.location.reload();
        }
    },
    computed: {
        weatherClass() {
            if (this.description && this.description.toLowerCase().includes('rain')) {
                return 'rain';
            } else if (this.temperature > 16) {
                return 'warm';
            } else {
                return '';
            }
        }
    }
};
</script>


<style>
body {
    background-color: #343d4b;
}

.weather-temp {
    margin: 0;
    font-weight: 700;
    font-size: 4em;
}

h2.mb-1.day {
    font-size: 3rem;
    font-weight: 400;
}

.main-div {
    border-radius: 20px !important;
    color: #fff !important;
    background-image: url(../assets/images/Snowy\ mountain\ landscape\ under\ a\ clear\ sky.png);
    background-size: cover;
    background-position: center !important;
    background-blend-mode: overlay !important;
    background-color: rgba(0, 0, 0, 0.5) !important;
    background-repeat: no-repeat;
    transition: 0.4s;
}

.main-div.warm {
    background-image: url(../assets/images/hot-bg.jpg);
}

.main-div.rain {
    background-image: url(../assets/images/3.jpg); /* Update this with your actual rain background image path */
}

.temp {
    position: absolute;
    bottom: 0;
}

.main-div:hover {
    transform: scale(1.1);
    transition: transform 0.5s ease;
    z-index: 1;
}

.card-2 {
    background-color: #212730 !important;
    border-radius: 20px !important;
}

.card-details {
    margin-left: 19px;
}

.h1_left {
    position: absolute;
    bottom: 25px;
    left: 16px;
    font-size: 3vw;
    line-height: 1.2;
}

.h3_left {
    position: absolute;
    left: 16px;
    font-size: 2vw;
    line-height: 0.5;
}

.h3_left small {
    font-size: 1rem;
}

table {
    position: relative;
    left: 15px !important;
    border-collapse: separate !important;
    border-spacing: 15px !important;
    width: 85% !important;
    text-align: left !important;
    max-width: 600px !important;
    margin: 0 auto !important;
}

th,
td {
    font-size: 18px !important;
    color: #fff !important;
}

td {
    text-align: right;
}

table,
tr:hover {
    color: red;
}

.change_btn {
    background-image: linear-gradient(to right, cyan, magenta);
}

.change_btn:hover {
    transform: scale(0.9);
    transition: transform ease;
}
</style>


