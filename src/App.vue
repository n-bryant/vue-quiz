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
            v-if="questions.length"
            :currentQuestion="questions[index]"
            :next="next"
            :incrementGuesses="incrementGuesses"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import { unescape } from "lodash";

import Header from './components/Header.vue';
import QuestionBox from './components/QuestionBox.vue';

const QUESTIONS_API_ENDPOINT = "https://opentdb.com/api.php?amount=10&category=27&type=multiple";

export default {
  name: 'app',
  components: {
    Header,
    QuestionBox
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
      totalGuesses: 0
    }
  },
  methods: {
    /**
     * Set the current question index to the next index
     */
    next() {
      this.index++;
    },
    /**
     * Increments the user's total guesses and correct guesses
     * @param {boolean} isCorrect - whether the submitted answer is correct
     */
    incrementGuesses(isCorrect) {
      if (isCorrect) {
        this.correctGuesses++;
      }
      this.totalGuesses++;
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
          question: unescape(result.question)
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
