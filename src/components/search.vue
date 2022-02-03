<template>
	<div class="input">
		<b-input-group class="input">
			<button
				class="inputpre"
				:style="[darkMode ? $store.state.dark : '']"
			>
				<img src="../assets/search.png" alt />
			</button>

			<v-select
				:class="darkMode ? 'vselect dark' : 'vselect'"
				placeholder="Search for location"
				style="border: none"
				v-model="selectedCity"
				@input="weatherLocation()"
				@search="onSearch"
				:options="cities"
				label="name"
				:filterable="false"
			>
				<template slot="no-options"
					>type to search for weather..</template
				>
				<template slot="option" slot-scope="option">
					<div v-bind:key="option.name" class="d-center">
						{{ option.name }}
					</div>
				</template>
				<template slot="selected-option" slot-scope="option">
					<div class="selected d-center">
						<div class="d-center">{{ option.name }}</div>
					</div>
				</template>
			</v-select>

			<button
				:style="[darkMode ? $store.state.dark : '']"
				@click="getLocation"
				class="inputpre"
			>
				<img src="../assets/target.png" alt />
			</button>

			<button
				:style="[darkMode ? $store.state.dark : '']"
				@click="resetapp"
				class="inputpre"
			>
				<img
					src="https://img.icons8.com/external-tal-revivo-bold-tal-revivo/24/000000/external-reload-turn-arrow-function-to-spin-and-restart-basic-bold-tal-revivo.png"
				/>
			</button>
		</b-input-group>
	</div>
</template>

<script>
import axios from 'axios';
import { themeConfig } from '../EventBus';

export default {
	data() {
		return {
			darkMode: false,
			selectedCity: '',
			cities: [],
			lat: '',
			lon: '',
		};
	},

	mounted() {
		themeConfig.$on('dark', (data) => {
			this.darkMode = data;
		});
	},

	methods: {
		// City Search
		onSearch(search, loading) {
			this.cities = [];
			if (search.length) {
				loading(true);
				this.search(loading, search, this);
			}
		},

		search(loading, search) {
			let cityarr = [];
			if (search != null && search.length >= 3) {
				axios
					.get(
						'http://dataservice.accuweather.com/locations/v1/cities/autocomplete?apikey=' +
							'G8JO484eu7AGsERAutseLf78kWsBdrWj' +
							'&language=' +
							'en-en' +
							'&q=' +
							search
					)
					.then((Response) => {
						let res = Response.data;
						loading(false);
						res.map((item) => {
							cityarr.push(item.LocalizedName);
						});
						this.cities = cityarr;
					})
					.catch((error) => {
						this.$notify({ type: 'error', text: error });
					});
			}
		},

		//With Select
		async weatherLocation() {
			let city = this.selectedCity;

			await axios(
				`https://api.openweathermap.org/data/2.5/forecast/daily?q=${city}&units=metric&appid=20571ab45c74dc2a1897b60c5b8047a1`
			).then((res) => {
				this.$emit('seven', res.data);
			});
			await axios(
				`https://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&appid=20571ab45c74dc2a1897b60c5b8047a1`
			).then((res) => {
				this.$emit('daily', res.data);
			});
			this.$emit('showCards');
		},

		//With Geolocation

		async getGeolocation(data) {
			await axios(
				`https://api.openweathermap.org/data/2.5/forecast/daily?lat=${data.coords.latitude}&lon=${data.coords.longitude}&units=metric&appid=20571ab45c74dc2a1897b60c5b8047a1`
			).then((res) => {
				this.$emit('seven', res.data);
			});

			await axios(
				`https://api.openweathermap.org/data/2.5/weather?lat=${data.coords.latitude}&lon=${data.coords.longitude}&units=metric&appid=20571ab45c74dc2a1897b60c5b8047a1`
			).then((res) => {
				this.$emit('daily', res.data);
			});

			this.$emit('showCards');
		},

		// Get User Location
		getLocation() {
			if (navigator.geolocation) {
				navigator.geolocation.getCurrentPosition((data) => {
					this.getGeolocation(data);
					this.$emit('showCards');
				});
			} else {
				console.log('Geolocation is not supported by this browser.');
			}
		},

		resetapp() {
			// this.$emit('seven', []);
			// this.$emit('daily', []);
			// this.$emit('showCards');

			// this.selectedCity = '';
			// this.cities = [];
			// this.lat = '';
			// this.lon = '';

			//bruteforced
			this.$router.go(0);
		},
	},
};
</script>

<style>
.vselect .vs__search::placeholder,
.vselect .vs__dropdown-toggle {
	background: white;
	border: none;
	border-radius: 0;
	width: 100%;
	height: 50px;
	color: rgba(0, 0, 0, 0.699);
}

.dark .vs__search::placeholder,
.dark .vs__dropdown-toggle,
.dark .vs__dropdown-menu {
	background: #303030;
	color: white !important;
	font-weight: 500;
}
</style>
