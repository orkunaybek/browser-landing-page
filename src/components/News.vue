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
							viewBox="0 0 60 60"
							style="enable-background:new 0 0 60 60;"
							xml:space="preserve"
						>
							<g>
								<path d="M31,16H17v14h14V16z M29,28H19V18h10V28z" />
								<path
									d="M52,16H36c-0.553,0-1,0.447-1,1s0.447,1,1,1h16c0.553,0,1-0.447,1-1S52.553,16,52,16z"
								/>
								<path
									d="M52,22H36c-0.553,0-1,0.447-1,1s0.447,1,1,1h16c0.553,0,1-0.447,1-1S52.553,22,52,22z"
								/>
								<path
									d="M52,28H36c-0.553,0-1,0.447-1,1s0.447,1,1,1h16c0.553,0,1-0.447,1-1S52.553,28,52,28z"
								/>
								<path
									d="M52,34H19c-0.553,0-1,0.447-1,1s0.447,1,1,1h33c0.553,0,1-0.447,1-1S52.553,34,52,34z"
								/>
								<path
									d="M34,40H19c-0.553,0-1,0.447-1,1s0.447,1,1,1h15c0.553,0,1-0.447,1-1S34.553,40,34,40z"
								/>
								<path
									d="M34,46H19c-0.553,0-1,0.447-1,1s0.447,1,1,1h15c0.553,0,1-0.447,1-1S34.553,46,34,46z"
								/>
								<path
									d="M34,52H19c-0.553,0-1,0.447-1,1s0.447,1,1,1h15c0.553,0,1-0.447,1-1S34.553,52,34,52z"
								/>
								<path d="M39,54h14V40H39V54z M41,42h10v10H41V42z" />
								<path
									d="M59,13c0-0.017-0.009-0.031-0.01-0.048V0H11v30H1v24c0,3.309,2.691,6,6,6h46c3.303,0,5.99-2.688,5.99-5.99V13.048
									C58.991,13.031,59,13.017,59,13z M56.99,2v10H13V2H56.99z M3,54V32h8v22c0,2.206-1.794,4-4,4S3,56.206,3,54z M53,58H11.469
									c0.016-0.018,0.027-0.039,0.042-0.057c0.204-0.233,0.39-0.482,0.556-0.745c0.027-0.042,0.052-0.084,0.078-0.128
									c0.162-0.27,0.305-0.552,0.423-0.848c0.016-0.041,0.03-0.082,0.045-0.123c0.115-0.306,0.209-0.622,0.273-0.949
									c0.006-0.03,0.008-0.061,0.014-0.09C12.962,54.716,13,54.362,13,54V30V14h43.99v40.01C56.99,56.21,55.2,58,53,58z"
								/>
								<path d="M47,4H23v6h24V4z M45,8H25V6h20V8z" />
							</g>
						</svg>
					</div>
					<h5 class="grid-title m-0">News</h5>
				</div>

				<hooper :settings="sliderSettings">
					<slide v-for="(article, index) in articles" :key="index">
						<div class="card news-slide h-100">
							<a :href="article.url" target="_blank"
								><img class="card-img-top" :src="article.urlToImage" alt=""
							/></a>
							<div class="card-body">
								<p class="article-title">{{ article.title }}</p>
							</div>
						</div>
					</slide>
					<hooper-pagination
						class="pagination"
						slot="hooper-addons"
					></hooper-pagination>
				</hooper>
			</div>
		</div>
	</div>
</template>

<script>
import axios from "axios";
import { Hooper, Slide, Pagination as HooperPagination } from "hooper";
import "hooper/dist/hooper.css";
export default {
	name: "News",
	components: { Hooper, Slide, HooperPagination },
	created() {
		this.request =
			"https://newsapi.org/v2/top-headlines?sources=bbc-news&apiKey=1bc87c9c2f554b4aa94f708308f94329";
		this.sendRequest();
	},
	data() {
		return {
			infoNews: {},
			news_api_key: "1bc87c9c2f554b4aa94f708308f94329",
			articles: [],
			source: "bbc-new",
			request: "",
			sliderSettings: {
				itemsToShow: 3
			}
		};
	},
	updated() {
		// Hooper.update();
	},

	methods: {
		newRequest() {
			this.request =
				"https://newsapi.org/v2/top-headlines?country=" +
				this.spurce +
				"&apiKey=" +
				this.news_api_key;
			this.sendRequest();
		},
		sendRequest() {
			axios
				.get(this.request)
				.then(response => {
					this.infoNews = response.data;
					this.articles = response.data.articles;
				})
				.catch(error => console.log(error));
		}
	},
	computed: {}
};
</script>

<style scoped>
.article-title {
	font-weight: 600;
	font-size: 0.9rem;
	line-height: 1.2;
}
.article-description {
	font-size: 0.9rem;
	font-family: "Times New Roman", Times, serif;
	line-height: 1.2;
}
.news-slide {
	margin: 0 5px;
}
.grid-item.card {
	border-radius: unset;
}
.grid-item > .card-body {
	background-color: rgba(0, 50, 125, 0.05);
}

.card {
	border-radius: 5px;
}
.pagination {
	bottom: -25px;
}

.hooper {
	height: 200px;
}

.hooper:focus {
	outline: none;
}
</style>
