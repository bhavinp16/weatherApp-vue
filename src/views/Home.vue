<template>
	<div class="container">
		<div class="header">
			<h1>{{ title }}</h1>
			<div class="switch">
				<input
					@input="(darkMode = !darkMode), modeChange()"
					v-model="darkMode"
					type="checkbox"
					id="toggle"
					class="toggle--checkbox"
				/>
				<label for="toggle" class="toggle--label"></label>
				<div class="background"></div>
			</div>
		</div>
	</div>
</template>

<script>
import { themeConfig } from '../EventBus';
export default {
	name: 'Home',
	data() {
		return {
			title: 'Weather App',
			message: 'Home Page',
			darkMode: false,
		};
	},
	methods: {
		modeChange() {
			themeConfig.$emit('dark', this.darkMode);
		},
	},
};
</script>

<style></style>
