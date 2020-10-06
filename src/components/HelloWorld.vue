<template>
  <h1>Speed Typer</h1>
  <div>
    <h3>Time: {{ time }}</h3>
    <h3>
      Score: {{ keywords.filter((keyword) => keyword.correct).length }} /
      {{ keywords.length }}
    </h3>
  </div>

  <p>
    <span
      :class="{
        correct: keyword.correct,
        wrong: keyword.wrong,
        pending: keyword.pending,
      }"
      :key="keyword.text"
      v-for="keyword in keywords"
      >{{ keyword.text }}{{ " " }}</span
    >
  </p>

  <input
    :disabled="!isButtonDisabled"
    ref="inputField"
    type="text"
    v-model="inputValue"
    @keyup.space="processInput($event)"
  />
  <button :disabled="isButtonDisabled" @click="startTimer()">Start</button>
  <button :disabled="!isButtonDisabled" @click="reloadPage()">Restart</button>
</template>

<script>
//var defaultKeywords = [];

export default {
  data() {
    return {
      axiostest: [],
      isButtonDisabled: false,
      time: 0,
      inputValue: "",
      index: 0,
      keywords: [],
      timer: "",
    };
  },
  methods: {
    processInput(event) {
      const value = event.target.value.trim();
      if (value === "") {
        return;
      }
      console.log(value);
      if (this.keywords[this.index].text === value) {
        this.keywords[this.index].correct = true;
        this.keywords[this.index].wrong = false;
        this.keywords[this.index].pending = false;
      } else {
        this.keywords[this.index].correct = false;
        this.keywords[this.index].wrong = true;
        this.keywords[this.index].pending = false;
      }
      this.inputValue = "";
      this.index++;
      if (this.index === this.keywords.length) {
        this.stopTimer();
        console.log("STOP TIMER!");
      }
    },
    startTimer: function () {
      window.requestAnimationFrame(() => this.$refs.inputField.focus());
      this.isButtonDisabled = true;
      this.timer = setInterval(() => {
        this.time = this.time + 1;
      }, 1000);
    },
    stopTimer() {
      let tempValue = this.time;
      clearInterval(this.timer);
      this.time = tempValue;
      alert(
        "Your scrore is : " +
          tempValue * this.keywords.filter((keyword) => keyword.correct).length
      );
    },
    reloadPage() {
      window.location.reload();
    },
  },
  created() {
    this.axios
      .get(
        "https://api.allorigins.win/raw?url=https://loripsum.net/generate.php?p=1&l=medium&d=1&a=1",
        {}
      )
      .then((response) => {
        this.axiostest = response.data;
        var ar = this.axiostest.split(" ", 51);
        ar.shift();
        ar.slice(-1);
        this.keywords = ar.map((keyword) => {
          return {
            text: keyword,
            correct: false,
            wrong: false,
            pending: true,
          };
        });
        console.log(this.keywords);
      })
      .catch(function (error) {
        console.log(error);
      });
  },
};
</script>

<style scoped>
.pending {
  font-weight: bold;
}

.correct {
  font-weight: bold;
  color: green;
}

.wrong {
  font-weight: bold;
  color: red;
}
</style>