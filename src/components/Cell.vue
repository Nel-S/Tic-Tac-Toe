<script>
// Should this even be a component, considering it has no data itself and solely performs emissions?
export default {
	props: {
		row: Number,
		column: Number,
		disable: Boolean
	},
	emits: ["hasBeenSelected"],
	methods: {
		claim() {
			// this.disable = true;
			// console.log(`ID: ${[this.row, this.column]}`)
			this.$emit("hasBeenSelected", [this.row, this.column])
		}
	}
}
</script>

<template>
	<button :disabled="disable" @click="claim()">
		<slot></slot>
	</button>
</template>

<style scoped>
button {
	background-color: lightgray;
	font-size: xx-large;
	font-weight: 900;
	min-width: 70px;
	min-height: 70px;
}
</style>