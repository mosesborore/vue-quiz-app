<script setup>
import { ref } from 'vue'
import questions from '@/questions.json'

const topicsAvailable = ['space', 'geography', 'history', 'science', 'trivial']
const currentGameTopic = ref("history")
const currentQuestionIndex = ref(0)
const isAnswered = ref(false)
const selectedAnswerIndex = ref(null); // to track which answer was selected

const emits = defineEmits([
	"update-score",
	"game-over"
])

let score = 0

const getQuestionsByTopic = (topic) => {
	if (!topicsAvailable.includes(topic)) {
		return null
	}
	return questions.quiz[topic]
}

let quizCollection = getQuestionsByTopic(currentGameTopic.value)

const questionsByTopic = ref([])

if (!quizCollection) {
	console.log("No questions found for that topic")
} else {
	questionsByTopic.value = quizCollection
}

const checkAnswer = (index) => {
	selectedAnswerIndex.value = index
	isAnswered.value = true
	if(index === questionsByTopic.value[currentQuestionIndex.value].correctAnswer){
		score += 1
		emits("update-score")
	}

	setTimeout(()=>{
		if(currentQuestionIndex.value < questionsByTopic.value.length - 1 ){
			currentQuestionIndex.value += 1
			isAnswered.value = false
			selectedAnswerIndex.value = null
		}else{
			alert(`Quiz finished! Your ${score}`);
			resetQuiz()
		}

	}, 1000)

}
const resetQuiz = () => {
	currentQuestionIndex.value = 0; // Restart the quiz from the first question
	isAnswered.value = false;
	selectedAnswerIndex.value = null;
	emits("game-over")
};

const getAnswerClass = (index) => {
	if (isAnswered) {
		if (index === selectedAnswerIndex.value) {
			return index === questionsByTopic.value[currentQuestionIndex.value].correctAnswer ? "correct" : "incorrect"
		}
	}
	return ""
}
</script>
<template>
	<p id="quiz-number">Question: {{ currentQuestionIndex + 1 }}</p>
	<p id="quiz-topic">Topic: {{  currentGameTopic }}</p>

	<div id="quiz">
		<p id="quiz-statement">{{ questionsByTopic[currentQuestionIndex].question }}</p>

		<div class="choices">
			<ul>
				<li v-for="answer, index in questionsByTopic[currentQuestionIndex].answers" :key="answer" :id="index" :class="`${getAnswerClass(index)}`"
					@click="checkAnswer(index)">{{ answer }}</li>
			</ul>
		</div>
	</div>
</template>

<style scoped>
#quiz {
	display: flex;
	flex-direction: column;
	margin-top: 15px;
	padding: 10px;
	box-shadow: 2px 2px 2px 2px rgb(213, 200, 200);
}

#quiz-statement {
	font-size: 2em;
	margin: 10px;
	letter-spacing: 1.4px;
}

#quiz-number {
	font-size: 1.5em;
	margin: 15px;
	text-align: center;
	letter-spacing: 2px;

}
#quiz-topic {
	font-size: 1.5em;
	font-weight: bold;
	margin: 15px;
	text-align: center;
	letter-spacing: 2px;

}

ul {
	list-style: none;
	margin-left: 0px;
}

li {
	margin: 10px;
	border: 1px;
	border-style: solid;
	padding: 6px;
	font-size: 1.3em;
}
</style>