<template>
      <div class="days-tab text-center">
        <div v-if="loading" class="loading">Loading...</div>
        <ul v-else class="p-0">
            <li v-for="day in forecast" :key="day.date" class="li_active">
                <div class="py-3"><img :src="day.iconurl" alt="weather icon"></div>
                <div class="py-3">{{ getDayName(day.date) }}</div>
                <div class="py-3">{{ day.temperature }}&deg;c</div>
            </li>
        </ul>
      </div>    
</template>
  
<script>
import axios from 'axios';
import moment from 'moment';

export default {
    props: {
        cityname: String
    },
    data() {
        return {
            forecast: [],
            loading: true,
            iconurl: null,
        };
    },
    mounted() {
        this.fetchWeatherData();
    },
    methods: {
        async fetchWeatherData() {
            const apikey = '94401aff94104727b56d78eb1d6f8f51';
            const city = this.cityname;
            const apiurl = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${apikey}`;

            await axios.get(apiurl).then(Response => {
                const forecastData = Response.data.list; 
                const filteredData = forecastData.map(item => {
                    return{
                        date : moment(item.dt_txt.split(' ')[0]),
                        temperature: Math.round(item.main.temp),
                        description: item.weather[0].description,
                        iconurl: `https://openweathermap.org/img/w/${item.weather[0].icon}.png`,
                    };
                }).reduce((acc, item) => {
                    if(!acc.some(day => day.date.isSame(item.date, 'day'))){
                        acc.push(item);
                    }
                    return acc;
                }, []).slice(1, 5);


                console.log(filteredData, "working");
                this.forecast = filteredData;
                this.loading = false;
            }).catch(error => {
                console.error('Error fetching weather data: ', error);
                this.loading = false;
            });
            
        },
        getDayName(date){
            return date.format('ddd');
        }
    }
    

}
</script>
  
<style>

.days-tab{
    width: 90%;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    border-radius: 20px;
    width: 90%;
    margin: auto;
}

.loading{
    color: #fff;
}

ul{
    margin: 0;
}

li{
    display: inline-block;
    list-style: none;
    height: 100%;
    width: 21%;
    max-width: 21%;
    font-size: 1vw;
    line-height: 1.2;
}

span{
    display: block;
    margin-bottom: 5px;
    font: 100% sans-serif;
    height: 35px;
}

.li_active{
    background: #253d5c;
    color: #222831;
    border-radius: 10px;
    margin: 0.5rem;
    color: #fff;
    font-weight: 600;
}

.li_active:hover{
    transform: scale(1.2);
    transition: transform 0.1s ease;
}

.li_active_temp{
    display: inline-block;
    background-color: #222831;
    color: #fff;
    transition: background-color 0.5s;
    border-radius: 10px;
}

.li_active_temp:hover{
    transform: scale(1.2);
    transition: transform 0.1s ease;
    background: #fff;
    border-radius: 10px;
    color: #191a1f;
}

</style>
  