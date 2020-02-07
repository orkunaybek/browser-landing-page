<template>
	<div @click="toggleVisibility" class="forecast-detail">
		<div class="row align-items-center">
			<div class="col-6 mb-2 p-2">
				<div class="badge badge-info p-1">
					{{ takeDay(listItem[0].dt_txt) }}
				</div>
			</div>

			<transition name="fade">
				<div
					v-if="!isVisible"
					class="col-6 d-flex align-items-center detail-row"
				>
					<div class="max-temp col-4">
						{{ celcius(findMax) }}
					</div>
					<div class="min-temp col-4">
						{{ celcius(findMin) }}
					</div>
					<div
						:class="findAverageWeather"
						class="weather-img wi-right col-4"
					></div>
				</div>
			</transition>
		</div>
		<transition name="fade">
			<div class="row detail-row" v-show="isVisible">
				<div class="col-2" v-for="(day, index) in listItem" v-bind:key="index">
					<div class="text-center">{{ day.dt_txt.slice(11, 16) }}</div>
					<div
						class="weather-img"
						:class="day.weather[0].main.toLowerCase()"
					></div>
					<div class="text-center">{{ celcius(day.main.temp) }}</div>
					<div class="weather-img"></div>
				</div>
			</div>
		</transition>
	</div>
</template>

<script>
export default {
	name: "ForecastItem",
	components: {},
	props: {
		listItem: Array,
		celcius: Function,
		takeDay: Function
	},
	data() {
		return {
			isVisible: false,
			hourlyForecasts: []
		};
	},
	methods: {
		toggleVisibility() {
			this.isVisible = !this.isVisible;
		}
	},
	computed: {
		findMax() {
			var list = [];
			this.listItem.forEach(function(item) {
				list.push(parseInt(Math.round(item.main.temp)));
			});
			return Math.max(...list);
		},
		findAverageWeather() {
			var list = [];
			this.listItem.forEach(function(item) {
				list.push(item.weather[0].main);
			});

			var mf = 1;
			var m = 0;
			var item;
			for (var i = 0; i < list.length; i++) {
				for (var j = i; j < list.length; j++) {
					if (list[i] == list[j]) m++;
					if (mf < m) {
						mf = m;
						item = list[i];
					}
				}
				m = 0;
			}
			return item.toLowerCase();
		},

		findMin() {
			var list = [];
			this.listItem.forEach(function(item) {
				list.push(parseInt(Math.round(item.main.temp)));
			});
			return Math.min(...list);
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

.weather-img.wi-right {
	background-position: calc(100% - 5px) center !important;
}

.main-day {
	font-size: 20px;
}
.forecast-detail {
	padding: 10px;
	transition: all 0.3s ease;
	border-radius: 15px;
}
.forecast-detail:hover {
	box-shadow: 0 0 15px 5px rgba(0, 0, 0, 0.045);
}

.fade-enter-active,
.fade-leave-active {
	transition: opacity 0.5s;
	max-height: 1000px;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
	opacity: 0;
	max-height: 0;
}
.detail-row {
	transition: all 0.7s ease-in-out;
}
</style>
