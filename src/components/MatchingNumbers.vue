<script setup>
import { ref, onMounted } from 'vue';
import anime from 'animejs/lib/anime.es.js';

const props = defineProps({
	numbersCount: {
		type: Number,
		default: 8,
		validator(value) {
			return value >= 2 && value <= 10; // arbitrary choice: numbers array length is between 2 and 10,
		},
	},
	pairSum: {
		type: Number,
		default: 21,
	},
	// While there isn't any specific requirement about not allowing duplicate numbers in the array, I've decided to put this as an option,
	noDuplicates: {
		type: Boolean,
	},
});

const numbers = ref([]);
const combinations = ref([]);

onMounted(() => {
	createNumbersArray();
});

function createNumbersArray() {
	let randomNumbers = [];

	for (let i = 0; i < props.numbersCount; i++) {
		let number;

		do {
			// generate a random number lower than the sum value. In this way every number in the array is a possible solution.
			number = Math.floor(Math.random() * props.pairSum);
		} while (props.noDuplicates && randomNumbers.includes(number));

		randomNumbers[i] = number;

		// anime({
		// 	targets: `.numbers-array li[data-index="${i}"]`,
		// 	translateY: 250,
		// 	easing: 'easeInOut',
		// });
	}

	numbers.value = randomNumbers;
}
// Storing a sorted version of the numbers array can help find combinations in an optimized way.
// But I've opted for a hash map approach with the unsorted array, because it requires less code.
// Using an hash map to store indexes of every number in the array, we can easily search for existing complements (number - sum value) using a single for loop.
// O(n) complexity
function findCombinations() {
	const pairs = [];
	const numbersMap = new Map();

	for (let i = 0; i < numbers.value.length; i++) {
		const number = numbers.value[i];
		const index = i + 1;

		const complement = props.pairSum - number;
		if (numbersMap.has(complement)) {
			pairs.push([numbersMap.get(complement), index]); // one solution found: get the pair of indexes
		}

		numbersMap.set(number, index);
	}

	combinations.value = pairs;
}
</script>

<template>
	<div class="matching-numbers-wrapper shadow-sm p-4 mb-5 bg-body rounded">
		<h1 class="h5 fw-bold text-center pb-2 border-bottom">
			Matching Numbers
		</h1>

		<div
			class="numbers-array border border-3 border-dark-subtle rounded p-2 mt-4 mb-2 text-center"
		>
			<ul class="list-inline m-0">
				<li
					v-for="(number, i) in numbers"
					:key="`${number}-${i}`"
					class="list-inline-item"
				>
					{{ number }}
				</li>
			</ul>
		</div>

		<div class="d-grid gap-2 col-8 mx-auto py-2">
			<button
				type="button"
				class="btn btn-primary"
				@click="createNumbersArray()"
			>
				Create New Array
			</button>
			<button
				type="button"
				class="btn btn-outline-primary"
				@click="findCombinations()"
			>
				Find Combinations
			</button>
		</div>

		<div class="mt-3">
			<h2 class="h5 fw-bold">Combinations:</h2>

			<ul v-if="combinations.length" class="combinations-list ps-3">
				<li
					v-for="(combination, i) in combinations"
					:key="`${combination}-${i}`"
					class="py-1"
				>
					{{ combination[0] }} & {{ combination[1] }}
				</li>
			</ul>
			<span v-else>No combination found 😞</span>
		</div>
	</div>
</template>

<style lang="scss" scoped>
.matching-numbers-wrapper {
	width: 25rem;
}

.numbers-array li:not(:last-child)::after {
	content: ',';
}

// change combinations list style to "dot"
.combinations-list li {
	list-style: none;
	font-size: 1rem;

	&::before {
		content: '·';
		font-size: 2rem;
		vertical-align: top;
		line-height: 1.2rem;
		padding-right: 0.3rem;
	}
}
</style>
