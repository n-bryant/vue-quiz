<template>
  <div>
      <b-jumbotron>
        <template slot="header">Finished!</template>

        <template slot="lead">
          Let's see how you did:
        </template>

        <hr class="my-4">

        <p>You correctly guessed <span>{{ correctGuesses }}</span> out of <span>{{ questionCount }}</span>!</p>

        <b-button variant="primary" v-on:click="setQuizCompleted">Play Again</b-button>
      </b-jumbotron>
    </div>
</template>

<script>
  export default {
    props: {
      // count of user's correct guesses
      correctGuesses: Number,
      // count of questions
      questionCount: Number
    },
    methods: {
      /**
       * Sets the quiz completed status to false
       */
      setQuizCompleted() {
        this.$emit("set-quiz-completed", false);
      }
    }
  }
</script>