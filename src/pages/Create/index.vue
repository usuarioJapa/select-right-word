<template>
<div class="container column">
  <div class="item">
    <h2>Create</h2>
  </div>
  <div class="item item-border">
    <Answers :exercise="exercise" ref="video"/>
  </div>
  <div class="item item-border">
    <div class="container">
      <div class="item flex-basis-500">
        <div class="container row">
          <div class="item flex-basis-50">
            <h4>Image</h4>
          </div>
          <div class="item flex-basis-45">
            <h4>Audio</h4>
          </div>
          <div class="item flex-basis-200">
            <h4>Phrase</h4>
          </div>
          <div class="item flex-basis-50">
            <h4>Options</h4>
          </div>
        </div>
        <div class="container row" v-for="(qst, index) in exercise.questions">
          <div class="item flex-basis-50">
            <img :src="qst.img">
          </div>
          <div class="item flex-basis-45">
            <button class="btn small" @click="$refs[`question-audio-${index}`][0].play()" :ref="`play-${index}`">Play</button>
            <button class="btn small" @click="$refs[`question-audio-${index}`][0].pause()" :ref="`pause-${index}`" style="display: none">Pause</button>
            <audio :src="qst.src" :ref="`question-audio-${index}`" @playing="$refs[`play-${index}`][0].style[`display`] = `none`; $refs[`pause-${index}`][0].style[`display`] = `block`" @pause="$refs[`pause-${index}`][0].style[`display`] = `none`; $refs[`play-${index}`][0].style[`display`] = `block`"
            />
          </div>
          <div class="item flex-basis-200">
            <span>{{ qst.title }}</span>
          </div>
          <div class="item flex-basis-50">
            <button class="btn small btn-primary" @click="edit(qst, index)" :disabled="isToUpdate">Edit</button>
            <button class="btn small btn-danger" @click="remove(index)" :disabled="isToUpdate">Remove</button>
          </div>
        </div>
      </div>
      <div class="item flex-basis-500">
        <div class="container column">
          <div class="item flex-basis-500">
            <div class="container">
              <div class="item flex-basis-150">
                <label for="exercise_title">Title</label>
              </div>
              <input id="exercise_title" class="input" type="text" v-model="exercise.title" required/>
            </div>
            <div class="container">
              <div class="item flex-basis-150">
                <label for="exercise_video">Video Source</label>
              </div>
              <input id="exercise_video" class="input" type="text" v-model="exercise.src" @click="exercise.src = `https://www.blogger.com/video-play.mp4?contentId=c55baa6a3a1305fd`" required/>
            </div>
            <form v-on:submit.prevent="addQuestion(question)">
              <div class="container">
                <div class="item flex-basis-150">
                  <label for="exercise_question_img">Character Image</label>
                </div>
                <input id="exercise_question_img" class="input" type="text" v-model="question.img" @click="question.img = `https://images.vexels.com/media/users/3/127025/isolated/preview/65b9b09e8a7b960d637ffd0a6e378575-walter-white-cartoon-character-by-vexels.png`" required/>
              </div>
              <div class="container">
                <div class="item flex-basis-150">
                  <label for="exercise_question_title">Phrase</label>
                </div>
                <input id="exercise_question_title" class="input" type="text" v-model="question.title" required/>
              </div>
              <div class="container">
                <div class="item flex-basis-150">
                  <label for="exercise_question_audio">Audio Source</label>
                </div>
                <input id="exercise_question_audio" class="input" type="text" v-model="question.src" @click="question.src = `https://vignette.wikia.nocookie.net/leagueoflegends/images/e/e6/Lux.move05.ogg/revision/latest?cb=20170310092306`" required/>
              </div>
              <div class="container">
                <div class="item flex-basis-150">
                  <label for="exercise_question_time">Time</label>
                </div>
                <input id="exercise_question_audio" class="input small" type="number" v-model="question.time" required>
                <button class="btn" @click.prevent="getCurrentTime()">Get Current</button>
              </div>
              <div class="container justify-content-center">
                <div class="item">
                  <button class="btn small btn-primary" type="submit" name="save_question" v-if="!isToUpdate">Add Question</button>
                  <button class="btn small btn-primary" v-else @click.prevent="update(question)">Edit Question</button>
                  <button class="btn small btn-danger" v-if="isToUpdate" @click.prevent="cancel()">Cancel Update</button>
                </div>
              </div>
            </form>
            <form v-on:submit.prevent="addWord(word)">
              <div class="container">
                <div class="item flex-basis-250">
                  <label for="exercise_question_word">Word</label>
                </div>
                <input class="input" id="exercise_question_word" type="text" v-model="word.word">
                <button class="btn btn-sm" type="submit" name="save_word">Add Word</button>
              </div>
              <div v-for="(word, index) in question.words">
                <input type="text" v-model="word.word">
                <input type="radio" name="correct" :checked="word.correct" @change="correctWordChanged(word)">
                <button @click.prevent="removeWord(index)">Remove</button>
              </div>
            </form>
            <div class="item full">
              <button class="btn small btn-success" @click="save(exercise)">Save</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
<script>
import Answers from '../../components/Answers'

export default {
  name: "Create",
  data() {
    return {
      exercise: {},
      question: {
        title: 'Hello there. I am looking for a place to stay tonigh',
        img: `https://images.vexels.com/media/users/3/127025/isolated/preview/65b9b09e8a7b960d637ffd0a6e378575-walter-white-cartoon-character-by-vexels.png`,
        src: `https://vignette.wikia.nocookie.net/leagueoflegends/images/8/87/CorporateMundo.attack6.ogg/revision/latest?cb=20120615005157`,
        playing: false,
        answered: false,
        time: 3,
        words: [
          {
            word: 'Place',
            correct: true
          },
          {
            word: 'Plate',
            correct: false
          },
          {
            word: 'Lace',
            correct: false
          },
          {
            word: 'Haze',
            correct: false
          },
          {
            word: 'Chase',
            correct: false
          }
        ]
      },
      word: {
        word: '',
        correct: false
      }
    }
  },
  methods: {
    save(exercise) {
      if (exercise.questions.length === 0) alert('Questions must be inserted before save!')
      else this.$store.commit('setExercise', exercise)
    },
    addQuestion(question) {
      if (question.words.length === 0) alert('Words must be informed!')
      else {
        let hasCorrect = question.words.findIndex(x => x.correct)
        if (hasCorrect < 0) alert('Correct Word must be selected!')
        else {
          this.exercise.questions.push(question)
          this.question = {
            src: ``,
            img: ``,
            playing: false,
            answered: false,
            words: []
          }
          this.exercise.ended = false
        }
      }
    },
    addWord(word) {
      this.question.words.push(word)
      this.word = {
        correct: false
      }
    },
    removeWord(index) {
      this.question.words.splice(index, 1)
    },
    correctWordChanged(word) {
      this.question.words.map(x => {
        if (x.word === word.word) x.correct = true
        else x.correct = false
      })
    },
    edit(question, id) {
      this.$store.commit('setToUpdate', id)
      this.question = question
    },
    update(question) {
      this.$store.commit('update', question)
      this.cancel()
    },
    remove(id) {
      this.exercise.questions.splice(id, 1)
    },
    cancel() {
      this.question = {
        src: ``,
        img: ``,
        playing: false,
        answered: false,
        words: []
      }
      this.$store.commit('setToUpdate', null)
    },
    getCurrentTime () {
      console.log(this.$refs['video'].getCurrentTime())
    }
  },
  computed: {
    isToUpdate() {
      return this.$store.getters['isToUpdate']
    }
  },
  components: {
    Answers
  },
  mounted () {
    this.exercise = this.$store.getters['getExercise']
  }
}
</script>
<style lang="scss" scoped>
img {
    width: 100%;
}

.btn.small {
    padding: 5px;
    font-size: 12px;
    margin-bottom: 5px;
    min-width: 60px;
}

.input.small {
  width: 55%;
}
</style>
