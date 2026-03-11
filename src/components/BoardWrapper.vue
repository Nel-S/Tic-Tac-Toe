<script>
import Board from "./Board.vue"

export default {
	data() {
		return {
			width: 3,
			height: 3,
			requiredInARow: 3,
			playersCount: 2,

			winningPlayer: null,
		}
	},
	components: {
		Board
	},
	computed: {
		getWinMessage() {
			return this.winningPlayer != null ? `${this.winningPlayer} has won!` : null;
		}
	},
	methods: {

		// reset(rows, columns) {
		// 	this.cells.length = 0;
		// 	let id = 0;
		// 	for (let row = 0; row < rows; ++row) {
		// 		const newArray = []
		// 		for (let column = 0; column < columns; ++column) {
		// 			newArray.push({id: id++, claimedBy: "​"})
		// 		}
		// 		this.cells.push(newArray)
		// 	}
		// }
	},

	mounted() {
		// this.reset(this.height, this.width);
	}
}
</script>

<template>
	<div class="box">
		<Board class="board" :width="this.width" :height="this.height" :requiredInARow="this.requiredInARow" :playersCount="this.playersCount" @hasWinner="(winner) => this.winningPlayer = winner"/>
		<!-- Should this be made its own component? -->
		<div class="winBox">
			<!-- <span>{{ this.getWinMessage() }}</span> -->
			<span class="winText">{{  this.winningPlayer == null ? "" : `${this.winningPlayer} has won!`}}</span> <!-- TODO: Also announce draws -->
		</div>
		<!-- This should probably be made its own component. -->
		<div class="settings">
			<input v-on:input="() => winningPlayer = null" v-model="this.width" type="number" id="width" name="width" min="1" max="15" />
			<label class="settingsText" for="width">Grid Width</label>
			<br />
			<input v-on:input="() => winningPlayer = null" v-model="this.height" type="number" id="height" name="height" min="1" max="15" />
			<label class="settingsText" for="height">Grid Height</label>
			<br />
			<input v-on:input="() => winningPlayer = null" v-model="this.requiredInARow" type="number" id="width" name="width" min="1" max="15" /> <!-- Is there a way to dynamically update the max based off of max(width, height)? -->
			<label class="settingsText" for="width">Required Cells in a Row</label>
			<br />
			<input v-on:input="() => winningPlayer = null" v-model="this.playersCount" type="number" id="width" name="width" min="1" max="53" /> <!-- Is there a way to pull the max straight from Board.allPossiblePlayers.length? -->
			<label class="settingsText" for="width">Number of Players</label>
		</div>
		<!-- I should probably also add a "Reset Current Game" button. -->
	</div>
</template>

<style scoped>
.board {
	padding-top: 10%;
}
.box {
	text-align: center;
	/* background-color: white; */
	margin-left: 20%;
	margin-right: 20%;
}
.winBox {
	background-color: red;
	/* padding-top: 1%; */
	/* padding-bottom: 1%; */
	border-radius: 30px;
}
.winText {
	color: white;
	font-size: xx-large;
	font-weight: bold;
}
.settings {
	background-color: #626262;
	border-radius: 30px;
	margin: auto;
	text-align: left;
}
.settingsText {
	color: #eee;
	font-size: large;
	font-weight: bold;
}
</style>