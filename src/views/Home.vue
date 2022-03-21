<template>
	<div>
		<div class="spacer"/>
		<h1>Tweet Counter</h1>
		<p>Enter a word or phrase</p>
		<Input @obtainedTweetCount="showData" @loading="loading"/>
		<Results v-if="showResults" :tweetCount="tweetCounts" :errMessage="errMessage" :timelineData="timelineData" />
		<p v-if="showLoading">loading</p>
		<LineChart v-if="timelineData" :chartData="chartData" :options="chartOptions" />
	</div>
</template>

<script>
import Input from '../components/Home/Input.vue'
import Results from '../components/Home/Results.vue'
import { LineChart } from 'vue-chart-3';
import { Chart, registerables, Utils } from "chart.js";

Chart.register(...registerables);

export default {
	components: { Input, Results, LineChart },
	data() {
		return {
			showResults: false,
			showLoading: false,
			tweetCounts: 0,
			errMessage: null,
			timelineData: null,
			dateOptions: { month: "numeric", day:"numeric", weekday: "short", hour: "numeric" },
			chartData: null,
			chartOptions: null
		}
	},
	methods: {
		loading() {
			this.showResults = false
			this.showLoading = true
		},
		showData({score, timeline, errMessage}){
			console.log(timeline);
			
			// Get arrays for the chart
			if(timeline){
				var startDates = timeline.map((dayInfo) => {
					return Date.parse(dayInfo.start)
				})
				console.log(startDates);
				var dates = []

				startDates.forEach(startDate => {
					var date = new Date(startDate).toLocaleString("en-US", this.dateOptions)
					dates.push(date)
				})
				console.log(dates);

				var tweetNums = timeline.map((dayInfo) => {
					return dayInfo.tweet_count
				})

				this.chartData = {
					labels: dates,
					datasets: [
						{
							label: 'Tweets',
							data: tweetNums,
							borderColor: "#6998AB",
							backgroundColor: "#6998AB",
						}
					]
				}

				// Set chart Options
				this.chartOptions = {
					scales: {
						x: {
							ticks: {
								maxTicksLimit: 21
							}
						}
					},
				}
				
			}
			

			//
			this.timelineData = timeline
			this.tweetCounts = score
			this.errMessage = errMessage
			this.showLoading = false
			this.showResults = true

		}
	}
}
</script>

<style>
.spacer {
	height: 80px;
}
</style>