<template>
	<h1 v-if="!errMessage">This has been tweeted {{ tweetCount.toLocaleString("en-US") }} times in the past week</h1>
	<p class="error" v-else>{{ errMessage }}</p>
</template>

<script>
export default {
	props: ['tweetCount', 'errMessage', 'timelineData'],
	data() {
		return {
			dateOptions: { month: "numeric", day:"numeric", weekday: "short" },
			
		}
	},
	methods: {

	},
	computed: {

	},
	mounted() {

	}
}
</script>

<style>
.error {
	color: red;
}
</style>