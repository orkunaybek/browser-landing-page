<template>
	<div id="app">
		<div class="weather-card page-container">
			<div class="row align-items-start">
				<WeatherCard />
				<News />
			</div>
		</div>
	</div>
</template>

<script>
import WeatherCard from "./components/WeatherCard";
import News from "./components/News";
export default {
	name: "app",
	components: { WeatherCard, News }
};
</script>

<style>
#app {
}
</style>
