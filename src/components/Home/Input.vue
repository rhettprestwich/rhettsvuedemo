<template>
	<form @submit.prevent="recievedInput">
		<input type="text" class="textbox" v-model="phraseToSearch"/>
		<button class="getData">Get Data</button>
	</form>
</template>

<script>
export default {
	emits: ['obtainedTweetCount', 'loading'],
	data() {
		return {
			phraseToSearch: 'batman',
			countResponse: [],
			tweetCount: null,
			verifiedTweetCount: null,
			timeline: null
		}
	},
	methods: {
		recievedInput() {
			// Validate.
			if(!this.phraseToSearch) {
				this.$emit('obtainedTweetCount', { score: null, timeline: null,
				errMessage:'Must enter ...something...' })
				return
			}

			// Loading.
			this.$emit('loading')

			// Setup request URLs. (future: put this in an array and let a function handle all of them)
			// future look into url builders.
			var baseTweetCountUri = 'https://rhettstwitterpassthrough.azurewebsites.net/api/RhettsPassthrough'
			+ '?code=0hFu20PmvYhvbGL2OlkxSmBkTDx2T3oPzraYWDOrzx/j98ms1lF29w=='

			var baseCountQuery = encodeURIComponent('"' + this.phraseToSearch + '"')
			var verifiedCountQuery = encodeURIComponent('"' + this.phraseToSearch + '"' + ' is:verified')

			var totalCountUri = baseTweetCountUri + '&granularity=hour&query=' + baseCountQuery
			var verifiedCountUri = baseTweetCountUri + '&granularity=hour&query=' + verifiedCountQuery

			// Make requests.
			Promise.all([
				fetch(totalCountUri, { method: "GET", headers: { "Accept": "application/json" }}),
				fetch(verifiedCountUri, { method: "GET", headers: { "Accept": "application/json" }}),
			]) // Catch errors/Get json. (future: can I move this down?)
			.then(([totalCountResponse, verifiedCountResponse]) => {

				if(totalCountResponse.ok && verifiedCountResponse.ok){
					return Promise.all([ totalCountResponse.json(), verifiedCountResponse.json()])
				}
				else{
					throw Error(response.statusText)
				}

			}) // Emit data.
			.then(([totalCountData, verifiedCountData]) => {
				console.log(verifiedCountData);
				this.tweetCount = totalCountData.meta.total_tweet_count
				this.verifiedTweetCount = verifiedCountData.meta.total_tweet_count
				this.timeline = totalCountData.data

				this.$emit('obtainedTweetCount', { 
					score:this.tweetCount, 
					verifiedCount:this.verifiedTweetCount, 
					timeline:this.timeline, 
					errMessage:null 
				})

			}) // Catch Errors.
			.catch(err => {

				console.log(err.message)
				this.$emit('obtainedTweetCount', { 
					score: null, 
					verifiedCount: null, 
					timeline: null,
					errMessage:'Twitter API Error, try a different word. Note: does not work with stop words like "the", "a", etc.' 
				})

			})
		},
	}
}
</script>

<style>
.getData {
	background-color: #406882;
	border-radius: 8px;
	border-style: none;
	box-sizing: border-box;
	color: #FFFFFF;
	cursor: pointer;
	display: inline-block;
	font-family: "Haas Grot Text R Web", "Helvetica Neue", Helvetica, Arial, sans-serif;
	font-size: 18px;
	font-weight: 500;
	height: 50px;
	line-height: 20px;
	list-style: none;
	margin: 20px;
	outline: none;
	padding: 10px 25px;
	position: relative;
	text-align: center;
	text-decoration: none;
	transition: color 100ms;
	vertical-align: baseline;
	user-select: none;
	-webkit-user-select: none;
	touch-action: manipulation;
	vertical-align: middle;
}

.getData:hover,
.getData:focus {
	background-color: #6998AB;
}

.textbox {
	border-top-left-radius: 30px;
	border-bottom-left-radius: 30px;
	box-sizing: border-box;
	border: 1px solid #848484;
	height:50px;
	max-width: 500px;
	width: 100%;
	padding-left:30px;
	font-size: 20px;
	vertical-align: middle;
}
</style>