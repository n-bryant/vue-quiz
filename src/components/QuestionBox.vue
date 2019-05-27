<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead">
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4">

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="answer"
          @click="selectAnswer(index)"
          :class="getAnswerClass(index)"
          :disabled="hasGuessed"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        :disabled="selectedIndex === null || hasGuessed"
        @click="submitAnswer"
      >
        Submit
      </b-button>
      <b-button
        @click="getNextQuestion"
        variant="success"
        :disabled="!hasGuessed"
      >
        Next Question
      </b-button>
    </b-jumbotron>
  </div>
</template>

<script>
  import { shuffle } from "lodash";
  import he from "he";

  export default {
    props: {
      // an object containing data for the current question
      currentQuestion: Object,
      // whether the question is the final question
      isFinalQuestion: Boolean
    },
    data() {
      return {
        // the index of the currently selected answer
        selectedIndex: null,
        // a shuffled array of answers
        shuffledAnswers: [],
        // the index of the correct answer
        correctIndex: null,
        // whether the user has submitted a guess
        hasGuessed: false
      }
    },
    // watch changes for props and run provided method(s) on change for that prop
    watch: {
      currentQuestion: {
        // call the handler upon initial receipt of the currentQuestion prop
        immediate: true,
        /**
         * Clear the selection and shuffle the answers for the current question
         */
        handler() {
          // clear selection when the question is changed
          this.selectedIndex = null;
          // clear the guess status
          this.hasGuessed = false;
          // shuffle answers
          this.shuffleAnswers();
        }
      }
    },
    methods: {
      /**
       * Updates the selectedIndex value to the index of the user's currently selected answer
       * @param {int} index - the index of the selected answer
       */
      selectAnswer(index) {
        this.selectedIndex = index;
      },
      /**
       * Sets shuffledAnswers to a shuffled array of answers created from the list of incorrect answers and the correct answer
       */
      shuffleAnswers() {
        const answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
        // answers are provided as html entity encoded strings from opentdb, so we decode them here
        const decodedAnswers = answers.map(answer => he.unescape(answer));
        this.shuffledAnswers = shuffle(decodedAnswers);
        this.correctIndex = this.shuffledAnswers.findIndex(answer => answer === he.unescape(this.currentQuestion.correct_answer));
      },
      /**
       * Set class names for the answer item based on guess status
       * @param {int} index - the index of the answer item
       * returns string
       */
      getAnswerClass(index) {
        let answerClass = "";
        
        if (!this.hasGuessed && this.selectedIndex === index) {
          // set class to 'selected' for this answer if a user hasn't guessed yet
          answerClass = "selected";
        } else if (this.hasGuessed && this.correctIndex === index) {
          // set class to 'correct' for this answer if it is the correct answer
          answerClass = "correct";
        } else if (
            this.hasGuessed && this.correctIndex !== index  && this.selectedIndex === index
          ) {
          // set class to 'incorrect' for this answer if it is not the correct answer
          answerClass = "incorrect";
        }

        return answerClass;
      },
      /**
       * Handles the submission of an answer
       */
      submitAnswer() {
        // store whether the selected answer is correct
        let isCorrect = false;
        if (this.selectedIndex === this.correctIndex) {
          isCorrect = true;
        }
        // denote that the user has submitted a guess for the question
        this.hasGuessed = true;
        // update guess counts
        this.incrementGuesses(isCorrect);
      },
      /**
       * Emit guess update
       * @param {boolean} isCorrect - whether the user's guess is correct
       */
      incrementGuesses(isCorrect) {
        this.$emit("increment-guesses", isCorrect);
      },
      /**
       * Request next question
       */
      getNextQuestion() {
        if (this.isFinalQuestion) {
          this.setQuizCompleted();
        }
        this.$emit("next-question");
      },
      /**
       * Sets the quiz completed status to true
       */
      setQuizCompleted() {
        this.$emit("set-quiz-completed", true);
      }
    }
  }
</script>

<style scoped>
  .list-group {
    margin-bottom: 1.5rem;
  }
  .list-group-item {
    cursor: pointer;
  }
  .list-group-item:hover {
    background: #e9ecef;
    color: #2c3e50;
  }
  .btn {
    margin: 0 .25rem;
  }
  .btn:disabled {
    cursor: not-allowed;
  }
  .selected, .selected:hover {
    background: #007bff;
    color: #fff;
  }
  .correct {
    background: #28a745;
    color: #fff;
  }
  .incorrect {
    background: #e03131;
    color: #fff;
  }
</style>