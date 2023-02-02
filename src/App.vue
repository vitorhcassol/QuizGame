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
      <Option @send-answer="this.sendAnswer" :answers="this.answers">
      </Option>
    
      <!-- Próxima Questão -->
      <section id="proximo" v-show="nextQuestion">
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

    <!-- Link para o repositório do GitHub -->
    <a href="https://github.com/vitorhcassol" target="_blank"><img src="@/assets/github-round-svgrepo-com.svg" alt="simbolo do GitHub, a silhueta da face de um gato"></a>
  </div>
</template>

<script>

import StartScreen from '@/components/StartScreen.vue'
import Score from '@/components/Score.vue'
import Option from '@/components/Option.vue'
import GameOver from '@/components/GameOver.vue'

export default {
  components: {
    Score, GameOver, StartScreen, Option
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
    /* Retorna um array com as respostas de maneira sortida */
    answers() {
      let answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },

  methods: {
    /* Inicia o jogo, escondendo a tela de star e buscando uma questão */
    startGame() {
      this.showStartScreen = false;
      this.showQuiz = true;
      this.getQuestion()
    },

    /* Busca uma questão e suas respectivas respostas */
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

    /* Compara a resposta selecionada com a resposta correta
    E retorna o feedback do acerto ou erro */
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

    /* Caso a resposta selecionada seja a correta
    Adiciona uma classe flash que muda o css da resposta */
    showCorrectAnswer() {
      let element = document.getElementById(this.correctAnswer);

      element.classList.add('flash');
    },

    /* Caso a resposta selecionada seja incorreta
    Adiciona uma classe error que muda o css da resposta */
    showIncorrectAnswer() {
      let element = document.getElementById(this.chosenAnswer);

      element.classList.add('error');
    },

    /* Após uma resposta ser selecionada
    esta função desabilita os botões de resposta */
    disableButtons() {
      let options = document.querySelectorAll('.option');

      options.forEach(element => {
        element.setAttribute("disabled", "");
      })
    },

    /* Seleciona todos os botões e retira seus estilos de acerto
    erro ou disabled */
    resetButtons() {
      let options = document.querySelectorAll('.option');

      options.forEach(element => {
        element.removeAttribute("disabled");
        element.classList.remove('error');
        element.classList.remove('flash');
      })
    },

    /* Reinicia o jogo settando todos os dados como 
    no modo inicial */
    tryAgain() {
        this.score = [];
        this.hit = 0;
        this.miss = 0;
        this.result = ''
        this.resetButtons();
        this.getQuestion();
    },

    /* Assim que a aplicação é carregada uma questão já é
    imediatamente buscada */
    created() {
      this.getQuestion();
    }
  },

  watch: {
    /* Método responsável por monitorar a quantidade de erros
    Assim que 5 erros forem cometidos, a tela modal de fim de jogo 'Derrota' é apresentada */
    miss(newMiss) {
      if(newMiss === 5) {
        this.result = 'LOSE'
        this.showModal = true;
      }
    },

    /* Método responsável por monitorar a quantidade de acertos
    Assim que 5 acertos forem cometidos, a tela modal de fim de jogo "Vitória" é apresentada */
    hit(newHit) {
      if(newHit === 5) {
        this.result = 'WIN'
        this.showModal = true;
      }
    }
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

  .col-a {
    display: flex;
    flex-direction: column;

    justify-content: center;
    align-items: center;

    grid-area: A;

    font-size: 2.1rem;
    font-weight: medium;
    text-align: center;

    gap: 3rem;
  }

  #proximo {
    display: flex;
    justify-content: flex-end;

    padding-right: 1.5rem;
  }

  #proximo button {
    color: var(--black);
    border: none;
    background-color: transparent;
    text-decoration: underline;
  }

  #proximo button:hover {
    cursor: pointer;
  }

  body img {
    width: 4rem;

    position: absolute;
    top: calc(100% - 5rem);
    right: calc(50% - 2rem);
  }
</style>