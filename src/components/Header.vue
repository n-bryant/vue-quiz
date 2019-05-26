<template>
  <div>
    <b-nav tabs>
    <b-nav-item disabled><strong>Fancy Quiz App</strong></b-nav-item>
    <b-nav-item disabled>Score: {{correctGuesses}}/{{totalGuesses}}</b-nav-item>
  </b-nav>
  </div>
</template>

<script>
  export default {
    props: {
      // count of correct guesses
      correctGuesses: Number,
      // count of total guesses
      totalGuesses: Number
    }
  }
</script>