<script setup>
import { ref } from 'vue'
import Quiz from './Quiz.vue';

const highScore = ref(0)
const score = ref(0)

const getHighScore = ()=>{
	const hs = localStorage.getItem("highScore")
	if (hs === null) return 0;
	return hs
}

highScore.value = getHighScore()

const updateScore = () => {
	score.value += 1
	if (score.value > highScore.value) {
		highScore.value = score.value
	}
}

const gameOver = () => {
	const hs = getHighScore()
	if (highScore.value > hs) {
		localStorage.setItem("highScore", highScore.value)
	}
}


</script>

<template>
	<header>
		<h1>Quiz App</h1>
		<div class="scores">
			<span id="score">Score: {{ score }}</span>
			<span id="high-score">High Score: {{ highScore }}</span>
		</div>
	</header>

	<Quiz v-on:update-score="updateScore" @game-over="gameOver" />
</template>
<style scoped>
header {
	display: flex;
	flex-direction: column;
	text-align: center;
}

h1 {
	margin: 20px;
	letter-spacing: 2px;
	font-size: 3em;
}

#score,
#high-score {
	margin-right: 10px;
	font-size: 1.5em;
	font-weight: bold;

}
</style>>