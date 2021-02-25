<template>
 <div class="quiz-box-container">
<b-jumbotron>
    <template #lead>
    {{currentQuestion.question}}
    </template>

    <hr class="my-4">

    <b-list-group>
      <b-list-group-item 
      v-for="(answer,index) in answers" 
      :key="index" 
      @click="selectAnswer(index)" 
      :class="answerClass(index)"
      >
        {{ answer }}
      </b-list-group-item>
    </b-list-group>
    <b-button 
			variant="primary" 
			@click="submitAnswer"
			:disabled="selectedIndex === null || answered"
		>
			Submit
		</b-button>
    <b-button 
			variant="success" 
			href="#" 
			@click="next"
		>
			Next
		</b-button>
  </b-jumbotron>
</div>
</template>

<script>
import _ from 'lodash';

export default {
  props: {
    currentQuestion:Object,
    next: Function,
		increment: Function,
  },
  data() {
    return {
      selectedIndex:null,
      shuffledAnswers:[],
			correctIndex:null,
			answered:false
    }
  },
  computed:{
		answers() {
			let answers =  [...this.currentQuestion.incorrect_answers]
      answers.push(this.currentQuestion.correct_answer)
      return answers
			}
		},// 2.method = > to get 1st questions answer's get shuffled. We can make watch method function to object
watch: {
      currentQuestion: {
				immediate:true,
				handler() {
					this.selectedIndex = null
					this.answered =false
					this.shuffleAnswers()
				}
			} 
    },
    // watch:{
    //   currentQuestion() {
    //     this.SelectedIndex = null
    //     this.shuffleAnswers()
    //   }
    // },
    methods: {
      selectAnswer(index) {
      this.selectedIndex = index
    },
		submitAnswer() {
			let isCorrect = false
			if(this.selectedIndex === this.correctIndex) {
				isCorrect = true
			}
			this.answered = true
			this.increment(isCorrect)
		},
    shuffleAnswers() {
				let answers =  [...this.currentQuestion.incorrect_answers,this.currentQuestion.correct_answer]
        this.shuffledAnswers= _.shuffle(answers)
				this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
		answerClass(index) {
			let answerClass = ''
						if (!this.answered && this.selectedIndex === index) {
							answerClass = 'selected'
						} else if (this.answered && this.correctIndex === index) {
							answerClass = 'correct'
						} else if (this.answered &&
							this.selectedIndex === index &&
							this.correctIndex !== index
						) {
							answerClass = 'incorrect'
						}
						return answerClass

		}
		// 1.method = > to get 1st questions answer's get shuffled.
		// mounted() {
		// 	this.shuffledAnswers()
		// }
  }
}
</script>

<style scoped>
  .list-group {
    margin-bottom:10px;
  }
  .list-group-item:hover {
    background-color: whitesmoke;
    cursor: pointer;
  }
  .btn {
    margin:0 15px;   
  }
.selected {
    background-color: lightskyblue;
  }
  .correct{
  background-color: aquamarine;
  }
  .incorrect{
    background-color: palevioletred;
  }
</style>
