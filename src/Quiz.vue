//HTML ALL IN HERE
<template>
  <div id ="app">

    <div v-if="preQuiz">
      <!-- pre quiz options -->
      <h1 style="titles"> Welcome to my Quiz </h1>
      <button v-on:click="beginQuiz()">Click to begin</button>
    </div>

    <div v-else-if="inQuiz">
      <div>
        <h1> Question {{ qIndex + 1 }} </h1>
        <h1> {{this.questions[this.qIndex].text}} </h1>
        <button v-on:click="picked(answer, firstClick)" v-for="answer in this.questions[this.qIndex].answers" v-bind:key="answer.id">{{answer}}</button>
      </div>
      <button @click = "nextQuestion(qIndex, hasAnswered)"> Click to view next question</button>
    </div>

    <div v-else-if="resultSummary" v-on="calculateCorrect(pickedAnswers)">
      <!-- show  number correct/incorrect-->

      <h1> You got {{this.numCorrect}} out of {{this.questions.length}}! </h1>
      <div  v-for="(question, i) in this.questions" v-bind:key="question.id">
          <h3> {{question.text}} </h3>
          <p> Your answer: {{pickedAnswers[i]}}</p>
          <p> Correct answer: {{question.correct}} </p>
      </div>
      <div v-if="this.numCorrect > 3">
        <h1> Good Job! </h1>
        <h1> Want to try again? </h1>
        <button v-on:click="beginQuiz()">Click to try again</button>
      </div>
      <div v-else>
        <h1> Better luck next time... </h1>
        <h1> Want to try again? </h1>
        <button v-on:click="beginQuiz()">Click to try again</button>
      </div>
    </div>

    <div v-else>
      <!-- should not be here-->
      alert("danger");
    </div>
  </div>
</template>

// JAVASCRIPT HERE
<script>
export default {
    props: {
      questions: {
        type: Array,
        required: true
      }
  },
  data() {
    return {
    preQuiz:true,
    inQuiz:false,
    resultSummary:false,
    hasAnswered:false,
    firstClick:true,
    qIndex:0,
    numCorrect:0,
    pickedAnswers:[]
    }
  },
  methods:{
    // Initalizes all variables and begins quiz
    beginQuiz() {
      this.preQuiz = false;
      this.inQuiz = true;
      this.qIndex = 0;
      this.numCorrect = 0;
      this.pickedAnswers = [];
      this.hasAnswered = false;
    },
    calculateCorrect(pickedAnswers) {
      let numCorrect=0;
      for (let i=0; i < this.questions.length; i++) {
        if(pickedAnswers[i] === this.questions[i].correct) {
          numCorrect++;
        }
      }
      this.numCorrect = numCorrect % 100;
    },
    // Used to increment question index to go to next question
    // sets firstClick to true again so answer can be recorded
    // transitions to summary after quiz
    nextQuestion(qIndex, hasAnswered) {
      // not at end of the quiz,
      // has answered current question
      if(hasAnswered && qIndex < (this.questions.length-1)) {
        this.qIndex=++qIndex;
        this.hasAnswered = false;
        this.firstClick = true;
        return this.qIndex;
      // has answered, at end
    } else if (hasAnswered && qIndex === (this.questions.length -1 )) {
        this.resultSummary = true;
        this.inQuiz = false;
        this.firstClick = true;
        return this.qIndex;
      // has not answered
      } else {
        alert("You must answer this question to proceed");
        return this.qIndex;
      }
    },
    // Used when an answer is picked to add to array of chosen answers
    // sets hasAnswered to true to allow user to move on
    // Need to only add picked answer if answer is final
    picked(answer, firstClick) {
      this.hasAnswered = true;
      // selected the right answer
      if(firstClick) {
        if (answer === this.questions[this.qIndex].correct) {
          this.pickedAnswers.push(answer);
          this.firstClick = false;
        } else {
          this.firstClick = false;
          this.pickedAnswers.push(answer);
          // alert(answer);
          // alert(this.questions[this.qIndex].correct);
          // this.pickedAnswers.push(this.questions[this.qIndex].answers[aIndex]);
        }
        // means not the first click so must remove previous choice from
        // picked answer array and add new choice
      } else {
        // correct answer
        if (answer === this.questions[this.qIndex].correct) {
          this.pickedAnswers.pop();
          this.pickedAnswers.push(answer);
          // wrong answer
        } else {
          this.pickedAnswers.pop();
          this.pickedAnswers.push(answer);
      }
    }
  }
}
};


</script>

//CSS GOES HERE
<style>
* {
  font-family: arial;
}
#app {
  text-align: center;
  color:#203e48;
  padding: 30px 0;
  background-color: #dff7f7;
}
.border {
  border-style: solid;
}
button {
  background-color: #ff7341;
  color: white;
  text-align: center;
  font-size: 16px;
  font-weight: bold;
  padding: 15px 32px;
  margin-right: 5px;
  margin-bottom: 15px;
  border: none;
  box-shadow: 0 12px 16px 0 rgba(0,0,0,0.24), 0 17px 50px 0 rgba(0,0,0,0.19);
}
</style>
