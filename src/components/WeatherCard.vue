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

				<div class="row align-items-center my-2 main-day">
					<div v-if="loading" class="lds-ring m-auto">
						<div></div>
						<div></div>
						<div></div>
						<div></div>
					</div>
					<div class="col-4">{{ city }}</div>
					<div v-if="currentWeather.temp" class="col-4 text-center">
						{{ fToCel(currentWeather.temp) }}
					</div>
					<div
						class="city-img col-4"
						:class="this.currentWeather.weather.toLowerCase()"
					></div>
				</div>
				<input
					v-on:keyup.enter="cityRequest()"
					class="form-control"
					v-model="api_city"
					placeholder="Şehir adı girin"
				/>

				<div
					@click="arrowStyler()"
					:style="arrowStyle"
					class="detail-arrow col-12"
				>
					<svg
						class="detail-arrow"
						version="1.1"
						id="Layer_1"
						xmlns="http://www.w3.org/2000/svg"
						xmlns:xlink="http://www.w3.org/1999/xlink"
						x="0px"
						y="0px"
						viewBox="0 0 491.996 491.996"
						style="enable-background:new 0 0 491.996 491.996;"
						xml:space="preserve"
					>
						<g>
							<g>
								<path
									d="M484.132,124.986l-16.116-16.228c-5.072-5.068-11.82-7.86-19.032-7.86c-7.208,0-13.964,2.792-19.036,7.86l-183.84,183.848
			L62.056,108.554c-5.064-5.068-11.82-7.856-19.028-7.856s-13.968,2.788-19.036,7.856l-16.12,16.128
			c-10.496,10.488-10.496,27.572,0,38.06l219.136,219.924c5.064,5.064,11.812,8.632,19.084,8.632h0.084
			c7.212,0,13.96-3.572,19.024-8.632l218.932-219.328c5.072-5.064,7.856-12.016,7.864-19.224
			C491.996,136.902,489.204,130.046,484.132,124.986z"
								/>
							</g>
						</g>
					</svg>
				</div>

				<transition name="fade">
					<div v-show="isDetail" class="details">
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
				</transition>
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
		setTimeout(this.newRequest, 100);
		setTimeout(this.sendRequest, 100);
	},
	mounted() {},
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
			request: "",
			isDetail: false,
			arrowStyle: "transform:rotate(0deg)",
			loading: "false"
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
			this.loading = true;
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
			this.loading = false;
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
		},
		arrowStyler() {
			if (!this.isDetail) {
				this.arrowStyle = "transform:scaleY(-1)";
			} else {
				this.arrowStyle = "transform:scaleY(1)";
			}
			this.isDetail = !this.isDetail;
			return this.arrowStyle;
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
	}
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

.detail-arrow {
	display: block;
	width: 18px;
	margin: 20px auto 0 auto;
	fill: #eab643;
}
.fade-enter-active,
.fade-leave-active {
	transition: all 0.5s;
	max-height: 1000px;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
	opacity: 0;
	max-height: 0;
}

.detail-arrow {
	transition: all 0.3s ease;
}

.lds-ring {
	display: inline-block;
	position: relative;
	width: 80px;
	height: 80px;
}
.lds-ring div {
	box-sizing: border-box;
	display: block;
	position: absolute;
	width: 64px;
	height: 64px;
	margin: 8px;
	border: 8px solid #eab643;
	border-radius: 50%;
	animation: lds-ring 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
	border-color: #eab643 transparent transparent transparent;
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
</style>
