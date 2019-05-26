<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead">
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4">

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="answer"
          @click="selectAnswer(index)"
          :class="[selectedIndex === index ? 'selected' : '']"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>
      <b-button variant="primary" href="#">Submit</b-button>
      <b-button @click="next" variant="success" href="#">Next Question</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
  import _ from "lodash";

  export default {
    props: {
      currentQuestion: Object,
      next: Function
    },
    data() {
      return {
        selectedIndex: null,
        shuffledAnswers: []
      }
    },
    computed: {
      answers() {
        let answers = [...this.currentQuestion.incorrect_answers];
        answers.push(this.currentQuestion.correct_answer);
        return answers;
      }
    },
    // watch changes for props and run provided method on change for that prop
    watch: {
      currentQuestion: {
        immediate: true,
        handler() {
          // clear selection when the question is changed
          this.selectedIndex = null;
          // shuffle answers
          this.shuffleAnswers();
        }
      }
    },
    methods: {
      async selectAnswer(index) {
        this.selectedIndex = index;
        await this.$nextTick();
      },
      shuffleAnswers() {
        let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
        this.shuffledAnswers = _.shuffle(answers);
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
  }
  .btn {
    margin: 0 .25rem;
  }
  .selected, .selected:hover {
    background: #007bff;
    color: #fff;
  }
  .correct {
    background: green;
  }
  .incorrect {
    background: red;
  }
</style>