<template>
    <div>
        <section class="hero is-primary">
            <div class="hero-body">
                <div class="container has-text-centered">
                    <h2 class="title">
                        {{ survey.name }}
                    </h2>
                </div>
            </div>
        </section>
        <section class="section">
            <div class="container">
                <div class="columns">
                    <div class="column is-10 is-offset-1">
                        <div v-bind:key="question.id" v-for="(question, idx) in survey.questions" v-show="currentQuestion === idx">
                            <!-- new v-show directive -->
                            <div class="column is-offset-3 is-6">
                                <!-- <h4 class='title'>{{ idx }}) {{ question.text }}</h4> -->
                                <h4 class="title has-text-centered">
                                    {{ question.text }}
                                </h4>
                            </div>
                            <div class="column is-offset-4 is-4">
                                <div class="control">
                                    <div v-bind:key="choice.id" v-for="choice in question.choices">
                                        <label class="radio">
                                            <input :value="choice.id" type="radio" v-model="selectedChoice">
                                                {{ choice.text }}
                                            </input>
                                        </label>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <!-- new pagination buttons -->
                        <div class="column is-offset-one-quarter is-half">
                            <nav aria-label="pagination" class="pagination is-centered" role="navigation">
                                <a @click.stop="goToPreviousQuestion" class="pagination-previous">
                                    <i aria-hidden="true" class="fa fa-chevron-left">
                                    </i>
                                    Back
                                </a>
                                <a @click.stop="goToNextQuestion" class="pagination-next">
                                    Next
                                    <i aria-hidden="true" class="fa fa-chevron-right">
                                    </i>
                                </a>
                            </nav>
                        </div>
                        <!-- new submit button -->
                        <div class="column has-text-centered">
                            <a @click.stop="handleSubmit" class="button is-focused is-primary is-large" v-if="surveyComplete">
                                Submit
                            </a>
                        </div>
                    </div>
                </div>
            </div>
        </section>
    </div>
</template>
<script>

export default {  
  data() {
    return {
      currentQuestion: 0  // new data prop
    }
  },
  beforeMount() {
    this.$store.dispatch('loadSurvey', { id: parseInt(this.$route.params.id) })
  },
  methods: { // new Vue obj member
    goToNextQuestion() {
      if (this.currentQuestion === this.survey.questions.length - 1) {
        this.currentQuestion = 0
      } else {
        this.currentQuestion++
      }
    },
    goToPreviousQuestion() {
      if (this.currentQuestion === 0) {
        this.currentQuestion = this.survey.questions.length - 1
      } else {
        this.currentQuestion--
      }
    },
    handleSubmit() {
      this.$store.dispatch('addSurveyResponse')
        .then(() => this.$router.push('/'))

      saveSurveyResponse(this.survey)
        .then(() => this.$router.push('/'))
    }
  },
  computed: {  // new Vue obj member
    surveyComplete() {
      if (this.survey.questions) {
        const numQuestions = this.survey.questions.length
        const numCompleted = this.survey.questions.filter(q => q.choice).length
        return numQuestions === numCompleted
      }
      return false
    },
    survey() {
      return this.$store.state.currentSurvey
    },
    selectedChoice: {
      get() {
          const question = this.survey.questions[this.currentQuestion]
          return question.choice
      },
      set(value){
          const question = this.survey.questions[this.currentQuestion]
          this.$store.commit('setChoice', { questionId: question.id, choice: value })
      }
    }
  }
}
</script>
<style>
</style>