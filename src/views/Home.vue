<template>
	<div>
		<div v-if="(isMobile() && !showResults) || !isMobile()" class="spacer"/>
		<h1 class="title">Tweet Counter</h1>
		<p class="instructions">Enter a word or phrase</p>
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
			chartData: null,
			chartOptions: null,
			dateOptions: null
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

				if(this.isMobile()) {
					this.dateOptions = { month: "numeric", day:"numeric", weekday: "short"}
				}
				else {
					this.dateOptions = { month: "numeric", day:"numeric", weekday: "short", hour: "numeric" }
				}

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
				var maxTicks = this.isMobile() ? 7 : 21

				this.chartOptions = {
					scales: {
						x: {
							ticks: {
								maxTicksLimit: maxTicks
							}
						},
						y: {
							suggestedMin: 0
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

		},
		isMobile() {
			if(screen.width <= 760) {
				return true;
			}
			else{
				return false
			}
		}
	}
}
</script>

<style>
.spacer {
	height: 80px;
}
</style>