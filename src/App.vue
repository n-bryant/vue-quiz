<template>
  <div id="app">
    <Header
      :correctGuesses="correctGuesses"
      :totalGuesses="totalGuesses"
    />
    <b-container class="main-container">
      <b-row>
        <b-col sm="6" offset="3">
          <QuestionBox 
            v-if="questions.length && !isQuizComplete"
            @next-question="nextQuestion"
            @increment-guesses="updateGuesses"
            @set-quiz-completed="setQuizCompleted"
            :currentQuestion="questions[index]"
            :isFinalQuestion="index === questions.length - 1"
          />
          <CompletedView
            v-if="questions.length && isQuizComplete"
            @set-quiz-completed="setQuizCompleted"
            :correctGuesses="correctGuesses"
            :questionCount="questions.length"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import he from "he";

import Header from './components/Header.vue';
import QuestionBox from './components/QuestionBox.vue';
import CompletedView from './components/CompletedView.vue';

const QUESTIONS_API_ENDPOINT = "https://opentdb.com/api.php?amount=10&category=27&type=multiple";

export default {
  name: 'app',
  components: {
    Header,
    QuestionBox,
    CompletedView
  },
  data() {
    return {
      // list of questions
      questions: [],
      // current question index
      index: 0,
      // count of correct answers
      correctGuesses: 0,
      // count of total guesses
      totalGuesses: 0,
      // completed status of quiz
      isQuizComplete: false
    }
  },
  methods: {
    /**
     * Set the current question index to the next index
     */
    nextQuestion() {
      this.index++;
    },
    /**
     * Increments the user's total guesses and correct guesses
     * @param {boolean} isCorrect - whether the submitted answer is correct
     */
    updateGuesses(isCorrect) {
      if (isCorrect) {
        this.correctGuesses++;
      }
      this.totalGuesses++;
    },
    /**
     * Updates status of isQuizComplete
     * @param {boolean} status - status to set isQuizComplete to
     */
    setQuizCompleted(status) {
      this.isQuizComplete = status;
      // reset game state
      if (status === false) {
        this.index = 0;
        this.correctGuesses = 0;
        this.totalGuesses = 0;
      }
    }
  },
  mounted() {
    // Retrieve questions from opentdb
    fetch(QUESTIONS_API_ENDPOINT, {
      method: "get"
    })
    .then((response) => {
      return response.json();
    })
    .then((jsonData) => {
      // opentdb provides question text as html entity encoded strings,
      // so we decode the question text before setting the results into state
      const decodedQuestions = jsonData.results.map(result => (
        {
          ...result,
          question: he.unescape(result.question)
        }
      ));
      // set decoded questions into state
      this.questions = decodedQuestions;
    })
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.main-container {
  margin-top: 3rem;
}
</style>
