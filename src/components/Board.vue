<script>
import Cell from "./Cell.vue"

export default {
	data() {
		return {
			allPossiblePlayers: [
				"X", "O",
				"A", "B", "C", "D", "E", "F", "G", "H", "I", "J", "K", "L", "M", "N",
				"P", "Q", "R", "S", "T", "U", "V", "W", "Y", "Z",
				"a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n",
				"o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z",
				"What kind of game are you playing that needs 53 players?!"
			],
			currentPlayer: 0,
			// Is there a way to modify the actual requiredInARow/playersCount properties so
			// we don't have to have duplicate variables?
			boundedRequiredInARow: 3,
			boundedPlayersCount: 2,
			cells: []
		}
	},
	components: {
		Cell
	},
	props: {
		width: Number,
		height: Number,
		requiredInARow: Number,
		playersCount: Number
	},
	emits: [
		"hasWinner"
	],
	// None of the props seem to actually update unless I add watchers for them.
	watch: {
		width(newWidth) {
			this.reset(this.height, newWidth);
			// this.requiredInARow = Math.min(this.requiredInARow, newWidth);
		},
		height(newHeight) {
			this.reset(newHeight, this.width);
			// this.requiredInARow = Math.min(this.requiredInARow, newHeight);
		},
		requiredInARow(newRequiredInARow) {
			const maxDimension = Math.max(this.width, this.height);
			if (newRequiredInARow > maxDimension) this.reset(this.width, this.height);
			// this.boundedRequiredInARow = Math.min(newRequiredInARow, maxDimension);
		},
		playersCount(newPlayersCount) {
			if (newPlayersCount < this.currentPlayer + 1) this.reset(this.width, this.height);
			this.boundedPlayersCount = Math.min(newPlayersCount, this.allPossiblePlayers.length - 1, this.width*this.height);
		}

	},
	methods: {
		isValidCoordinate(row, column) {
			return row >= 0 && row < this.cells.length && column >= 0 && column < this.cells[row].length;
		},
		determineAction(cellCoordinates) {
			if (cellCoordinates == undefined || cellCoordinates.length < 2) throw new Error(`Invalid cell coordinates object received: ${cellCoordinates}.`);
			const row = cellCoordinates[0];
			const column = cellCoordinates[1];
			if (!this.isValidCoordinate(row, column)) throw new Error(`Invalid coordinates received: ${row}, ${column}.`);

			const cell = this.cells[row][column];
			cell.claimedBy = this.allPossiblePlayers[this.currentPlayer];
			cell.disable = true;
			this.currentPlayer = (this.currentPlayer + 1) % this.boundedPlayersCount;
			this.checkForVictory(row, column);	
		},

		checkForVictory(row, column) {
			if (!this.isValidCoordinate(row, column)) throw new Error(`Invalid coordinates received: ${row}, ${column}.`);
			const claimedPlayer = this.cells[row][column].claimedBy;
			for (let rowDx = -1; rowDx <= 0; ++rowDx) {
				for (let columnDx = -1; columnDx <= 1; ++columnDx) {
					if (!rowDx && !columnDx) return;

					let matches = 1;
					for (let direction = -1; direction <= 1; direction += 2) {
						for (let iterations = 1; iterations < this.requiredInARow; ++iterations) {
							const offsetRow = row + rowDx*iterations*direction;
							const offsetColumn = column + columnDx*iterations*direction;
							if (!this.isValidCoordinate(offsetRow, offsetColumn) || this.cells[offsetRow][offsetColumn].claimedBy != claimedPlayer) break;
							++matches;
						}
					}
					if (matches < this.requiredInARow) continue;
					this.endGame(claimedPlayer);
					return;
				}
			}
		},

		// I could announce draws by calling this with winningPlayer="No one",
		// although a dedicated custom message would be better.
		endGame(winningPlayer) {
			// console.log(`${winningPlayer} wins!`);
			for (const rowArray of this.cells) {
				for (const cell of rowArray) {
					cell.disable = true;
				}
			}
			this.$emit("hasWinner", winningPlayer);
		},

		reset(rows, columns) {
			this.currentPlayer = 0;
			this.cells.length = 0;
			let id = 0;
			for (let row = 0; row < rows; ++row) {
				const newArray = []
				for (let column = 0; column < columns; ++column) {
					newArray.push({id: id++, claimedBy: "​"}) // HACK because buttons deflate otherwise
				}
				this.cells.push(newArray)
			}
		}
	},

	mounted() {
		this.reset(this.height, this.width);
	}
}
</script>

<template>
	<!-- It might be wise to use a table instead, to avoid wrapping issues. -->
	 <!-- The players = 53 easter egg also breaks the grid. -->
	<div v-for="(row, rowIndex) in this.cells">
		<Cell v-for="(cell, cellIndex) in row" :disable="cell.disable" :row="rowIndex" :column="cellIndex" @hasBeenSelected="(cellCoordinates) => determineAction(cellCoordinates)" >{{ cell.claimedBy }}</Cell>
	</div>
</template>

<style scoped>
</style>