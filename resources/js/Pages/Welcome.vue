<template>
    <Head title="Weather - Advantage " />

    <!-- Header -->
    <div
        class="grid grid-flow-row justify-center min-h-screen bg-center bg-gray-200 selection:bg-indigo-600 selection:text-white">
        <!-- <div v-if="canLogin" class="sm:fixed sm:top-0 sm:right-0 p-6 text-right">
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
        </div> -->

        <!-- Main Content -->
        <div class="grid content-center">
            <input class="place-self-center mt-12 w-96 py-4 px-6 rounded-lg overflow-hidden border-2 border-indigo-600" type="text" v-model="queryValue"
                @keydown.enter="fetchWeather" placeholder="Insert city..">
            <div class="text-center">{{ errorMessage }}</div>
        </div>
        <div v-if="weather.value != ''"
            class="border border-indigo-600 grid grid-cols-8 shadow-lg  text-white max-w-5xl place-self-center overflow-hidden rounded-2xl w-full h-11/12"
            :class="weather.value.weather[0].main == 'Clouds' ? 'clouds' : weather.value.weather[0].main == 'Clear' ? 'clear' : ''">

            <!-- Left Side -->
            <div class="col-span-2 text-gray-100 bg-slate-950/30 px-8 pt-12 border-r border-gray-100 ">
                <div class="text-7xl mb-4 text-center">
                    <p class="text-white font-black">{{ Math.round(weather.value.main.temp - 273.15) }}<span
                            class="text-4xl">℃</span></p>
                </div>
                <ul class="list-inside list-disc">
                    <li>Wind: {{ weather.value.wind.speed }} km/h</li>
                    <li>Humidity: {{ weather.value.main.humidity }}%</li>
                    <li>Pressure: {{ weather.value.main.pressure }}</li>
                </ul>
                <div class="border border-gray-100 rounded-full my-12" />
                <div class="grid justify-center mb-8">
                    <img class="w-24" src="https://cdn-icons-png.flaticon.com/128/1146/1146885.png" alt="Uploaded image">
                    <div class="text-center text-xl">{{ dateBuilder(weather.value.sys.sunrise) }}</div>
                </div>
                <div class="grid justify-center mb-8">
                    <img class="w-24" src="https://cdn-icons-png.flaticon.com/128/1146/1146886.png" alt="Uploaded image">
                    <div class="text-center text-xl">{{ dateBuilder(weather.value.sys.sunset) }}</div>
                </div>
            </div>

            <!-- Right Side -->
            <div class="col-span-6 px-10 pt-12 bg-slate-950/10">
                <div class=" uppercase mb-16 text-center">National Weather</div>
                <div class="italic">Weather Forecast</div>
                <div class="text-6xl font-black mb-20">
                    <p class="inline">{{ weather.value.weather[0].main }}</p>
                    <img class="inline w-20" :src='`http://openweathermap.org/img/w/${weather.value.weather[0].icon}.png`'
                        alt="" srcset="">
                </div>
                <div class="text-3xl">
                    {{ weather.value.name }} - {{ weather.value.sys.country }}
                </div>
                <div class="italic mb-28">
                    {{ currentTime() }}
                </div>

                <div class="bg-gray-100 px-6 py-2 rounded-full max-w-fit text-gray-900 font-semibold">
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
const weather = reactive({
    value: ''
})
const errorMessage = ref('')

const fetchWeather = async () => {
    try {
        await fetch(`${baseURL}weather?q=${queryValue.value}&appid=${apiKey}`)
            .then(res => {
                console.log(res)
                return res.json();
            })
            .then((res) => {
                weather.value = res
                errorMessage.value = 'Success'
            })
    } catch (error) {
        alert(error)
        errorMessage.value = "Place is not found."
    }
}

const currentTime = () => {
    let d = new Date()
    let days = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
    let months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
    let day = days[d.getDay()]
    let month = months[d.getMonth()]
    let date = d.getDate()
    let year = d.getFullYear()
    return `${day}, ${month} ${date} ${year}`
}

const dateBuilder = (value) => {
    let timestampMilliseconds = value * 1000;
    let date = new Date(timestampMilliseconds);

    let hours = date.getHours()
    let minutes = date.getMinutes()
    return `${hours}:${minutes}`
}


</script>

<style>
.bg-dots-darker {
    background-image: #161616;
}

.clouds {
    @apply text-gray-900;
    background-image: url('https://images.pexels.com/photos/19670/pexels-photo.jpg?auto=compress&cs=tinysrgb&w=800&dpr=2');
}

.clear {
    @apply text-gray-100;
    background-image: url('https://s7d2.scene7.com/is/image/TWCNews/1031_nc_sunny_weather_3-1');
}
</style>
