<script setup>
import {ref,reactive,computed,watch,nextTick,onMounted} from 'vue'
const querySearch = ref("")
const convertWeather=(state)=>{
  const options= {'sunny':"icon-sunny.webp",'storm':"icon-storm.webp",'snow':"icon-snow.webp",'rain':'icon-rain.webp','partly-cloudy':'icon-partly-rain.webp','overcast':'icon-overcast.webp','fog':'icon-fog.webp','drizzle':'icon-drizzle.webp'}
  return options[state]
}

const current= reactive({
  location:'Hanoi, Vietnam',
  date:new Date(),
  temp:20,
  feel_like:21,
  humidity:2,
  wind:9,
  precipitation:18,
  weather:'sunny'
})
const daily = ref(['Mon','Tue','Wed','Thu','Fri','Sat','Sun'])
daily.value = daily.value.map((t)=>({id:t,max:20,min:18,weather:'sunny'}))
const obj = {}
for(let i = 0;i < 24;i++){
  obj[i] = {weather:'sunny',temp:18}
}
const hourly_daily = ref(null)
hourly_daily.value = daily.value.map((t)=>({
  id:t.id,
  ...obj
}))
const chosen_day= ref("Tue")
const filtered_chosen_day = computed(()=>
  hourly_daily.value.find((t)=>t.id === chosen_day.value)
)
const convertAMPM=(time)=>{
  if (time >=12) return time + " PM";
  return time + " AM";
}
const convertDate=(date)=>{
  const dictweek = ['Sunday','Monday','Tuesday','Thrusday','Wednesday','Friday','Saturday']
  const dictmonth = ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec']
  let dayofweek = dictweek[date.getDay()]
  let month = dictmonth[date.getMonth()]
  let dayofmonth= date.getDate()
  let year = date.getFullYear()
  return `${dayofweek}, ${month} ${dayofmonth}, ${year}`
} 
const convertShortToFull=(day)=>{
  const item={"Mon":"Monday","Tue":"Tuesday","Wed":"Wednesday","Thu":"Thursday","Fri":"Friday","Sat":"Saturday","Sun":"Sunday"}
  return item[day]
}  
</script>
<template>
<main>
  <section class="hero">
    <span class="hero__menu">
      <img src="public/logo.svg" width=197 height=40>
      <span class=hero__setting>
        <img src="public/icon-units.svg">
        Units
        <img src="public/icon-dropdown.svg">
      </span>
    </span>
    <h1 class="hero__msg">How's the sky looking today?</h1>
    <span class="hero__search">
      <span class="search">
        <img src="public/icon-search.svg">
        <input v-model.trim="querySearch" @keydown.enter="" placeholder="Search for a place...">
      </span>
      <button @click="">Search</button>
    </span>
  </section>
  <section class="content">
    <div class="left">
      <div class="current">
        <div class="current__detail bento-card">
          <div>
            <span>{{current.location}}</span>
            <span>{{convertDate(current.date)}}</span>
          </div>
          <div>
            <img :src="'public/'+convertWeather(current.weather)">
            <span>
            {{current.temp}}&deg;
            </span>
          </div>
        </div>
        <div class="current__additional">
          <span class="current__feel bento-card">
            <span>Feels like</span>
            <span>{{current.feel_like}}&deg;</span>
          </span>
          <span class="current__hum bento-card">
            <span>Humidity</span>
            <span>{{current.humidity}}%</span>
          </span>
          <span class="current__wind bento-card">
            <span>Wind</span>
            <span>{{current.wind}}mph</span>
          </span>
          <span class="current__pre bento-card">
            <span>Precipitation</span>
            <span>{{current.precipitation}}in</span>
          </span>
        </div>
      </div>
      <div class="daily">
        <h3>Daily forecast</h3>
        <ul>
          <li v-for="day in daily" :key="day.id" class='bento-card'>
            <span>{{day.id}}</span>
            <span><img :src="'public/'+convertWeather(day.weather)"></span>
            <span>
              <span>{{day.min}}&deg;</span>
              <span>{{day.max}}&deg;</span>
            </span>
          </li>

        </ul>
      </div>
    </div>
    <div class="right bento-card">
      <div class="right__banner">
        <span>Hourly forecast</span>
        <span class="bento-card">
          <span>{{convertShortToFull(chosen_day)}}</span>
          <img src='public/icon-dropdown.svg'>
        </span>
      </div>
      <div class="right__content">
        <div v-for="hour in 24" :key="hour" class="bento-card" >
          <img :src="'public/'+convertWeather(filtered_chosen_day?.[hour-1]?.weather)">
          <span>{{convertAMPM(hour-1)}}</span>
          <span>{{filtered_chosen_day?.[hour-1]?.temp}}&deg;</span>
        </div>
      </div>
    </div>
  </section>
</main>
</template>
