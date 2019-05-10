<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead">{{ currentQuestion.question }}</template>

      <hr class="my-4">

      <b-list-group>
        <b-list-group-item
          v-for="(answer,index) in shuffledAnswers"
          :key="index"
          @click.prevent="selectAnswer(index)"
          :class="answerClass(index)"
        >{{ answer }}</b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || justAnswered"
      >Submit</b-button>
      <b-button variant="success" href="#" @click="next">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      justAnswered: false
    };
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.shuffleAnswers();
        this.justAnswered = false;
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.justAnswered = true;
      this.increment(isCorrect);
    },
    answerClass(index) {
      let answerClass = "";

      if (!this.justAnswered && this.selectedIndex === index) {
        answerClass = "selected";
      } else if (this.justAnswered && this.correctIndex === index) {
        answerClass = "correct";
      } else if (
        this.justAnswered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = "incorrect";
      }
      return answerClass;
    }
  }
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}
.list-group-item {
  margin-bottom: 2px;
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