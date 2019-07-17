<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead">{{ currentQuestion.question }}</template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="answer"
          @click.prevent="selectAnswer(index)"
          :class="answerClass(index)"
        >{{ answer }}</b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
      >Submit</b-button>
      <b-button @click="next" variant="success">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
  export default {
    props: {
      currentQuestion: Object,
      next: Function,
      increment: Function
    },
    data() {
      return {
        selectedIndex: null,
        shuffledAnswers: [],
        answered: false,
        correctIndex: null
      };
    },
    computed: {
      answers() {
        const { incorrect_answers, correct_answer } = this.currentQuestion;
        return [...incorrect_answers, correct_answer];
      }
    },
    watch: {
      currentQuestion: {
        immediate: true,
        handler() {
          this.selectedIndex = null;
          this.shuffleAnswers();
          this.answered = false;
        }
      }
    },
    methods: {
      selectAnswer(index) {
        this.selectedIndex = index;
      },
      shuffleAnswers() {
        const { incorrect_answers, correct_answer } = this.currentQuestion;
        const answers = [...incorrect_answers, correct_answer];
        const shuffledAnswers = [];
        while (answers.length) {
          const index = Math.floor(Math.random() * answers.length);
          shuffledAnswers.push(...answers.splice(index, 1));
        }
        this.shuffledAnswers = shuffledAnswers;
        this.correctIndex = this.shuffledAnswers.indexOf(correct_answer);
      },
      submitAnswer() {
        const isCorrect =
          this.shuffledAnswers[this.selectedIndex] ===
          this.currentQuestion.correct_answer;
        this.increment(isCorrect);
        this.answered = true;
      },
      answerClass(index) {
        const { answered, correctIndex, selectedIndex } = this;
        return !answered && selectedIndex === index
          ? 'selected'
          : answered && correctIndex === index
          ? 'correct'
          : answered && selectedIndex === index && correctIndex !== index
          ? 'incorrect'
          : '';
      }
    },
    mounted() {
      this.shuffleAnswers();
    }
  };
</script>

<style scoped>
  .list-group {
    margin-bottom: 15px;
  }
  .list-group-item:hover {
    background: #eee;
    cursor: pointer;
  }
  .btn {
    margin: 0 5px;
  }
  .selected {
    background-color: lightblue;
  }
  .correct {
    background-color: lightgreen;
  }
  .incorrect {
    background-color: red;
  }
</style>
