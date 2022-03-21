<template>
	<form @submit.prevent="getDataClicked">
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
			tweetCount: null
		}
	},
	methods: {
		getDataClicked() {
			if(!this.phraseToSearch) {
				this.$emit('obtainedTweetCount', { score: null, timeline: null,
				errMessage:'Must enter ...something...' })
				return
			}

			this.$emit('loading')
			var uri = 'https://rhettstwitterpassthrough.azurewebsites.net/api/RhettsPassthrough'
			+ '?code=0hFu20PmvYhvbGL2OlkxSmBkTDx2T3oPzraYWDOrzx/j98ms1lF29w==&granularity=hour&query="' 
			+ encodeURIComponent(this.phraseToSearch) + '"'
			console.log("URI: " + uri);
			console.log("encoded URI: " + uri);

			fetch(uri, {
				method: "GET",
				headers: {
					"Accept": "application/json"		
				}
			})
			.then((res) => 
			{
				if(res.ok){
					return res.json()	
				}
				else{
					throw Error(response.statusText)
				}
			})
			.then(data => {
				//console.log("data:")
				//console.log(data)
				this.countResponse = data
				//console.log("countResponse:")
				//console.log(this.countResponse)

				this.tweetCount = this.countResponse.meta.total_tweet_count
				//console.log("tweetCount:")
				//console.log(this.tweetCount)
				//console.log(this.countResponse.data);
				//console.log("emiting")
				this.$emit('obtainedTweetCount', { score:this.tweetCount, timeline:this.countResponse.data, errMessage:null })
			})
			.catch(err => {
				console.log(err.message)
				this.$emit('obtainedTweetCount', { score: null, timeline: null,
				errMessage:'Twitter API Error, try a different word. Note: does not work with stop words like "the", "a", etc.' })
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