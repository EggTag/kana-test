<template>
  <div id="body">
    <h3><p>Choose Kana</p></h3>
    <p>
      <input type="radio" v-model="kana_mode" value="both" id="both-check" />
      <label for="both-check">Both</label>
    </p>
    <p>
      <input
        type="radio"
        v-model="kana_mode"
        value="hiragana"
        id="hiragana-check"
      />
      <label for="hiragana-check">Hiragana</label>
    </p>
    <p>
      <input
        type="radio"
        v-model="kana_mode"
        value="katakana"
        id="katakana-check"
      />
      <label for="katakana-check">Katakana</label>
    </p>

    <h3><p>Line</p></h3>
    <ul>
      <li v-for="line in lines" :key="line">
        <input
          type="checkbox"
          :id="line"
          :value="line"
          v-model="chosen_lines"
        />
        <label :for="line">{{ line }}</label>
      </li>
    </ul>

    <h3><p>Mode</p></h3>
    <p>
      <input type="radio" v-model="mode" value="read" id="reading-radio" />
      <label for="reading-radio">Reading</label>
    </p>
    <p>
      <input type="radio" v-model="mode" value="write" id="writing-radio" />
      <label for="writing-radio">Writing</label>
    </p>
    <h3><p>Quiz</p></h3>
    <p class="quiz">{{ quiz.q }}</p>

    <button v-on:click="nextQuiz()" >Next Quiz</button>
    <button v-on:click="show_ans = true">Correct</button>
    <h3><p>Answer</p></h3>
    <p v-if="show_ans" class="ans">{{ quiz.a }}</p>
    <p v-else class="ans">&nbsp;</p>
  </div>
</template>

<script>
import hiragana from "./hiragana.json";
import katakana from "./katakana.json";

export default {
    mounted() {
        document.addEventListener('keypress', (e) => {
            if(e.code == 'Space') {
                this.nextQuiz()
            }
            if(e.code == 'KeyC') {
                this.show_ans = true
            }
        });

    },
  data() {
    return {
      kana_mode: "both",
      hiragana,
      katakana,
      mode: "read",
      show_ans: false,
      quiz: {
        q: "",
        a: "",
      },
      lines: ["a", "k", "s", "t", "n", "h", "m", "y", "r", "w"],
      chosen_lines: ["a", "k", "s", "t", "n", "h", "m", "y", "r", "w"],
    };
  },
  methods: {
    nextQuiz: function () {
      if (this.quiz_pool.length == 0) {
        return;
      }
      let quiz_index = this.getRandomInt(this.quiz_pool.length);
      if (this.mode == "read") {
        this.quiz.q = this.quiz_pool[quiz_index].kana;
        this.quiz.a = this.quiz_pool[quiz_index].roumaji;
      } else {
        this.quiz.q = this.quiz_pool[quiz_index].roumaji;
        this.quiz.a = this.quiz_pool[quiz_index].kana;
      }
      this.show_ans = false;
    },
    getRandomInt: function (max) {
      return Math.floor(Math.random() * max);
    },
  },
  computed: {
    quiz_pool() {
      let result = [];
      let hiragana_pool = this.hiragana.filter(function (e) {
        return this.indexOf(e.line) >= 0;
      }, this.chosen_lines);
      let katakana_pool = this.katakana.filter(function (e) {
        return this.indexOf(e.line) >= 0;
      }, this.chosen_lines);
      if (this.kana_mode == "both") {
        result = [...hiragana_pool, ...katakana_pool];
      }
      if (this.kana_mode == "hiragana") {
        result = hiragana_pool;
      }
      if (this.kana_mode == "katakana") {
        result = katakana_pool;
      }
      return result;
    },
  },
};
</script>


<style scoped>
#body {
  padding: 10px;
}

.quiz {
  font-size: 4rem;
}

.ans {
  font-size: 4rem;
}
</style>
