<template>
    <Head title="Welcome" />

    <!-- Header -->
    <div
        class="relative sm:flex sm:justify-center sm:items-center min-h-screen bg-dots-darker bg-center bg-gray-100 dark:bg-dots-lighter dark:bg-gray-900 selection:bg-red-500 selection:text-white">
        <div v-if="canLogin" class="sm:fixed sm:top-0 sm:right-0 p-6 text-right">
            <Link v-if="$page.props.auth.user" :href="route('dashboard')"
                class="font-semibold text-gray-600 hover:text-gray-900 dark:text-gray-400 dark:hover:text-white focus:outline focus:outline-2 focus:rounded-sm focus:outline-red-500">
            Dashboard</Link>

            <template v-else>
                <Link :href="route('login')"
                    class="font-semibold text-gray-600 hover:text-gray-900 dark:text-gray-400 dark:hover:text-white focus:outline focus:outline-2 focus:rounded-sm focus:outline-red-500">
                Log in</Link>

                <Link v-if="canRegister" :href="route('register')"
                    class="ml-4 font-semibold text-gray-600 hover:text-gray-900 dark:text-gray-400 dark:hover:text-white focus:outline focus:outline-2 focus:rounded-sm focus:outline-red-500">
                Register</Link>
            </template>
        </div>

        <!-- Main Content -->
        <div class="grid grid-cols-8 text-white max-w-5xl bg-gray-300 overflow-hidden rounded-2xl w-full h-11/12">
            <input class="col-span-8 place-self-center my-12 w-96 py-4 px-6" type="text" v-model="queryValue"
                @keydown.enter="fetchWeather" placeholder="Insert city..">
            <div class="col-span-2 bg-purple-400 p-8 border-r border-gray-600">
                <div class="text-7xl mb-4">
                    {{ Math.round(weather.value.main.temp - 273.15) }} ℃
                </div>
                <ul class="list-inside list-disc">
                    <li>Wind: {{ weather.value.wind.speed }} km/h</li>
                    <li>Humidity: {{ weather.value.main.humidity}}</li>
                    <li>Pressure: {{ weather.value.main.pressure }}</li>
                </ul>

            </div>
            <div class="col-span-6 bg-white p-6 text-gray-800">
                <div class="font-bold uppercase mb-16 line-clamp-3">National<br>Weather</div>
                <div class="italic">Weather Forecast</div>
                <div class="text-6xl font-black mb-20">
                    <p class="inline">{{ weather.value.weather[0].main }}</p>
                    <img class="inline w-20" :src='`http://openweathermap.org/img/w/${weather.value.weather[0].icon}.png`' alt="" srcset="">
                </div>
                <div class="text-3xl">
                    {{ weather.value.name }} - {{ weather.value.sys.country }}
                </div>
                <div class="italic">
                    {{ dateBuilder() }}
                </div>

                <div class="bg-gray-100 px-6 py-2 rounded-full max-w-fit">
                    See Details
                </div>
            </div>
        </div>

    </div>
</template>

<script setup>
import { Head, Link } from '@inertiajs/vue3';
import { ref, reactive } from 'vue';

defineProps({
    canLogin: {
        type: Boolean,
    },
    canRegister: {
        type: Boolean,
    },
    laravelVersion: {
        type: String,
        required: true,
    },
    phpVersion: {
        type: String,
        required: true,
    },
});

const apiKey = '48049ea6ce90dfc3a76f3674c45a8d64'
const baseURL = 'https://api.openweathermap.org/data/2.5/'
const queryValue = ref('')
const weather = reactive({})

const fetchWeather = () => {
    console.log(queryValue.value)
    try {
        fetch(`${baseURL}weather?q=${queryValue.value}&appid=${apiKey}`)
            .then(res => {
                console.log("+++++++++++++++")
                return res.json();
            })
            .then((res) => {
                console.log("&&&&&&&&&&&&&&&&")
                console.log(weather.value)
                weather.value = res
                console.log(weather.value)
            })
    } catch (error) {
        console.log(error)
    }
}

const dateBuilder = () => {
    let d = new Date()
    let days = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
    let months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
    let day = days[d.getDay()]
    let month = months[d.getMonth()]
    let date = d.getDate()
    let year = d.getFullYear()
    return `${day}, ${month} ${date} ${year}`
}


</script>

<style>
.bg-dots-darker {
    background-image: #161616;
}
</style>
