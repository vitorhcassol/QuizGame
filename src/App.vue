<template>
  <div id="app">
    <!-- TELA DE START -->
    <StartScreen
    v-show="showStartScreen"
    @start-game="startGame()"
    ></StartScreen>

    <!-- TELA DE QUIZ -->
    <div
    v-show="showQuiz"
    class="card">
      <!-- Componente do placar e questão -->
      <section id="question" class="col-a">
        <Score :score="this.score" />
        <!-- Apresentando a questão -->
        <h2> Question: {{ question }}</h2>
      </section>
      <!-- Apresentando as opções -->
      <section id="options" class="col-b">
        <button
        v-for="(item, index) in this.answers" :key="index"
        class="option"
        :id="item"
        @click="sendAnswer(item)"
        >
          {{ item }}
        </button>
      </section>
    
      <!-- Feedback -->
      <section id="feedback" v-show="nextQuestion">
        <button @click="getQuestion(), resetButtons()"> Próxima questão </button>
      </section>
      <!-- Modal Game Over -->
      <transition name="modal">
        <GameOver v-if="showModal"
        @close="showModal = false"
        @try-again="tryAgain()"
        :result="result"
        :hit="hit"
        :miss="miss">
        </GameOver>
      </transition>
    </div>
  </div>
</template>

<script>

import Score from '@/components/Score.vue'
import GameOver from '@/components/GameOver.vue'
import StartScreen from '@/components/StartScreen.vue'

export default {
  components: {
    Score, GameOver, StartScreen,
  }, 

  data() {
    return {
      question: undefined,

      correctAnswer: undefined,
      incorrectAnswers: [],

      chosenAnswer: '',

      nextQuestion: false,

      score: [],
      hit: 0,
      miss: 0,

      showStartScreen: true,
      showQuiz: false,
      showModal: false,
      result: ''
    }
  },

  computed: {
    answers() {
      let answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },

  methods: {
    getQuestion() {
      this.question = undefined;
      this.chosenAnswer = undefined;
      this.nextQuestion = false;

      this.axios
      .get('https://the-trivia-api.com/api/questions?limit=1')
      .then((response) => {
        this.question = response.data[0].question;
        this.correctAnswer = response.data[0].correctAnswer;
        this.incorrectAnswers = response.data[0].incorrectAnswers;
      })
    },

    sendAnswer(item) {
      this.chosenAnswer = item;
      if(this.chosenAnswer === this.correctAnswer) {
        this.score.push('hit');
        this.hit++;
        this.showCorrectAnswer();
        this.disableButtons();
        this.nextQuestion = true;
      } else {
        this.score.push('miss');
        this.miss++;
        this.showCorrectAnswer();
        this.showIncorrectAnswer();
        this.disableButtons();
        this.nextQuestion = true;
      }
    },

    showCorrectAnswer() {
      let element = document.getElementById(this.correctAnswer);

      element.classList.add('flash');
    },

    showIncorrectAnswer() {
      let element = document.getElementById(this.chosenAnswer);

      element.classList.add('error');
    },

    disableButtons() {
      let options = document.querySelectorAll('.option');

      options.forEach(element => {
        element.setAttribute("disabled", "");
      })
    },

    resetButtons() {
      let options = document.querySelectorAll('.option');

      options.forEach(element => {
        element.removeAttribute("disabled");
        element.classList.remove('error');
        element.classList.remove('flash');
      })
    },

    tryAgain() {
        this.score = [];
        this.hit = 0;
        this.miss = 0;
        this.result = ''
        this.resetButtons();
        this.getQuestion();
    },

    startGame() {
      this.showStartScreen = false;
      this.showQuiz = true;
    }
  },

  watch: {
    miss(newMiss) {
      if(newMiss === 5) {
        this.result = 'LOSE'
        this.showModal = true;
      }
    },

    hit(newHit) {
      if(newHit === 5) {
        this.result = 'WIN'
        this.showModal = true;
      }
    }
  },

  created() {
    this.getQuestion()
  }
}

</script>

<style>

@import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');


  @font-face {
    font-family: 'Newake';
    src: url('@/assets/Newake-Font-Demo.otf') format('opentype');
    font-weight: normal;
    font-style: normal;
  }

  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  :root {
    /*  Paleta de cores  */
    
    --white: #ffffff;
    --black: #634347;
    --black-opacity: #63434719;
    --error: #BF6211;
    --success: #00D6A4;
    --primary-color: #CCDFEE;
    --secondary-color: #FF8747;
    --terciary-color: #FFEFB2;


    font-size: 62.5%; /* 1rem = 10px */
  }

  html,
  body {
    width: 100%;
    height: 100%;
  }

  body {
    display: flex;
    align-items: center;
    justify-content: center;

    padding: 1.5rem;

    font-size: 1.6rem;
    font-weight: normal;
    font-family: 'Poppins', sans-serif;

    background-color: var(--black);
    color: var(--black);
  }

  .card {
    max-width: 102.4rem;
    border-radius: 2.5rem;

    padding: 2.5rem 1.5rem 2.5rem 1.5rem;

    display: grid;
    grid-template-rows: 1fr;
    grid-template-areas: 
    'A'
    'B';

    background-color: var(--primary-color);
  }

  .col-a,
  .col-b {
    display: flex;
    flex-direction: column;

    justify-content: center;
    align-items: center;
  }

  .col-a {
    grid-area: A;

    display: flex;

    font-size: 2.1rem;
    font-weight: medium;
    text-align: center;

    gap: 3rem;
  }

  .col-b {
    grid-area: B;

    padding: 3rem 0rem;

    gap: 1.5rem;
  }

  .option {
    background-color: var(--white);
    color: var(--black);
    opacity: 0.8;
    border-radius: 1rem;
    border: none;

    padding: 1rem;

    min-width: 20rem;

    text-align: center;
    font-weight: bold;

    transition: opacity 0.16s, background-color 0.1, color 0.1;
  }

  .option:hover {
    cursor: pointer;
    opacity: 1;
  }

  .option:active {
    background-color: var(--black);
    color: var(--white);
  }

  #feedback {
    display: flex;
    justify-content: flex-end;

    padding-right: 1.5rem;
  }

  #feedback button {
    color: var(--black);
    border: none;
    background-color: transparent;
    text-decoration: underline;
  }

  #feedback button:hover {
    cursor: pointer;
  }

  .flash {
    animation-name: flash;
    animation-duration: 0.5s;
    animation-iteration-count: 3;

    background-color: var(--success);
    color: var(--white);
  }

  .error {
    background-color: var(--error);
    color: var(--white)
  }

  @keyframes flash {
    0% {
      background-color: var(--success);
      color: var(--white);
    }
    50% {
      background-color: var(--white);
      color: var(--black);
    }
    100% {
      background-color: var(--success);
      color: var(--white);
    }
  }
</style>