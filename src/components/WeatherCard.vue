<template>
	<div class="grid presentation col-12 col-md-6">
		<div class="grid-item card">
			<div class="card-body">
				<div class="d-flex align-items-center mb-3">
					<div class="grid-icon mr-3">
						<svg
							version="1.1"
							id="Capa_1"
							xmlns="http://www.w3.org/2000/svg"
							xmlns:xlink="http://www.w3.org/1999/xlink"
							x="0px"
							y="0px"
							viewBox="0 0 512 512"
							style="enable-background:new 0 0 512 512;"
							xml:space="preserve"
						>
							<g>
								<path
									d="M341.333,288.593V85.333C341.333,38.205,303.128,0,256,0s-85.333,38.205-85.333,85.333v203.259
											C144.48,312.03,128,346.091,128,384c0,70.693,57.308,128,128,128s128-57.307,128-128C384,346.091,367.52,312.03,341.333,288.593z
											M256,469.333c-47.128,0-85.333-38.205-85.333-85.333c0-24.637,10.441-47.492,28.455-63.615l14.212-12.72V85.333
											c0-23.564,19.103-42.667,42.667-42.667s42.667,19.102,42.667,42.667v222.332l14.212,12.72
											c18.014,16.123,28.455,38.977,28.455,63.615C341.333,431.128,303.128,469.333,256,469.333z"
								/>
							</g>
							<g>
								<rect x="234.667" y="170.667" width="42.667" height="256" />
							</g>
							<g>
								<circle cx="256" cy="384" r="64" />
							</g>
						</svg>
					</div>
					<h5 class="grid-title m-0">5 Days Forecast</h5>
				</div>
				<div class="row align-items-center my-4 main-day">
					<div class="col-4">{{ city }}</div>
					<div class="col-4 text-center">{{ fToCel(currentWeather.temp) }}</div>
					<div
						class="city-img col-4"
						:class="this.currentWeather.weather"
					></div>
				</div>
				<input
					v-on:keyup.enter="cityRequest()"
					class="form-control"
					v-model="api_city"
					placeholder="Şehir adı girin"
				/>
				<div
					class="justify-content-start"
					v-for="(listItem, index) in list"
					v-bind:key="index"
				>
					<ForecastItem
						:listItem="listItem"
						:celcius="fToCel"
						:takeDay="takeDay"
					/>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
// const API_KEY = "ab1f5ae8db3e7311b66dd9446baa3a41";
// let city = "edirne";
// const CURRENT_WEATHER = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&APPID=${API_KEY}`;

import axios from "axios";
import ForecastItem from "./ForecastItem";

export default {
	name: "WeatherCard",
	components: { ForecastItem },

	beforeMount() {
		this.getPos();
	},
	mounted() {
		setTimeout(this.newRequest, 100);
		setTimeout(this.sendRequest, 100);
	},
	data() {
		return {
			info: {},
			forecasts: [],
			city: "",
			currentWeather: {
				weather: "",
				temp: ""
			},
			api_city: "",
			api_key: "ab1f5ae8db3e7311b66dd9446baa3a41",
			api_long: "",
			api_lat: "",
			request: ""
		};
	},
	methods: {
		savePosition(position) {
			this.api_lat = Math.ceil(position.coords.latitude);
			this.api_long = Math.ceil(position.coords.longitude);
		},
		getPos() {
			navigator.geolocation.getCurrentPosition(this.savePosition);
		},
		newRequest() {
			this.request =
				"https://api.openweathermap.org/data/2.5/forecast?lat=" +
				this.api_lat +
				"&lon=" +
				this.api_long +
				"&APPID=" +
				this.api_key;

			this.sendRequest();
		},
		cityRequest() {
			this.request =
				"https://api.openweathermap.org/data/2.5/forecast?q=" +
				this.api_city +
				"&APPID=" +
				this.api_key;
			this.sendRequest();
		},
		sendRequest() {
			axios
				.get(this.request)
				.then(response => {
					this.city = response.data.city.name;
					this.info = response.data;
					this.forecasts = response.data.list;
					this.currentWeather.weather = response.data.list[0].weather[0].main;
					this.currentWeather.temp = response.data.list[0].main.temp;
				})
				.catch(error => console.log(error));
		},
		fToCel(value) {
			return Math.round(value - 273) + "°C";
		},
		takeDay(value) {
			var x = new Date(value.substr(0, 10));
			switch (x.getDay()) {
				case 1:
					value = "Monday";
					break;
				case 2:
					value = "Tuesday";
					break;
				case 3:
					value = "Wednesday";
					break;
				case 4:
					value = "Thursday";
					break;
				case 5:
					value = "Friday";
					break;
				case 6:
					value = "Saturday";
					break;
				case 0:
					value = "Sunday";
					break;
			}
			return value;
		}
	},
	computed: {
		list() {
			var x = {};
			for (let i = 0; i < this.forecasts.length; i++) {
				let key = this.forecasts[i].dt_txt.substr(0, 10);
				if (typeof x[key] == "undefined") x[key] = [];

				x[key].push(this.forecasts[i]);
			}
			return x;
		}
	},
	filters: {}
};
</script>

<style scoped>
/* Weather images */
.clouds.weather-img,
.clouds.city-img {
	background: url(../assets/svg/cloudy.svg);
}

.rain.weather-img,
.rain.city-img {
	background: url(../assets/svg/rainy.svg);
}

.snow.weather-img,
.snow.city-img {
	background: url(../assets/svg/snowy.svg);
}

.clear.weather-img,
.clear.city-img {
	background: url(../assets/svg/sunny.svg);
}

.weather-img {
	background-repeat: no-repeat !important;
	background-size: contain !important;
	background-position: center !important;
	height: 40px;
}
.city-img {
	background-repeat: no-repeat !important;
	background-size: contain !important;
	background-position: calc(100% - 15px) center !important;
	height: 60px;
}

.main-day {
	font-size: 20px;
}
</style>
