<script setup>
import MatchingNumbers from './components/MatchingNumbers.vue';
</script>

<template>
	<main class="main h-100">
		<div class="container-fluid">
			<div class="d-flex justify-content-center">
				<MatchingNumbers />
			</div>
		</div>
	</main>
</template>

<style lang="scss">
$primary: #5469e4; // custom bootstrap primary color

@import 'bootstrap/scss/bootstrap';

#app {
	height: 100vh;

	.main {
		padding: 8em 0;
		background-color: #f6f9fc;
	}
}
</style>
