<template>
  <section>
    <div class="options">
      <div class="coins">
        <!-- {{counter*100}} -->
      </div>
      <div></div>
    </div>
    <div>
      <div class="settings">
        <div class="setting-case">
          <label for="transform">Case</label>
          <select name="transform" id="transform" class="clear" v-model="transform">
            <option value="upper" selected="selected">UPPER</option>
            <option value="lower">lower</option>
            <option value="capital">Capital</option>
          </select>
        </div>

        <div class="setting-lang">
          <label for="language">Language</label>
          <select name="language" id="language" class="clear" v-model="language">
            <option
              :value="index"
              v-for="(voice,index) in eng_voices"
              :key="index"
              :selected="index==8"
            >{{voice.voiceURI}}-{{voice.lang}}</option>
          </select>
        </div>
      </div>
      <div :class="transform" class="words">{{words[counter]}}</div>
    </div>
    <div class="actions">
      <button v-if="counter<=0" class="clear" :disabled="true"></button>
      <button v-if="counter>0" @click="loadLastWord" class="outline">Last</button>
      <button v-if="words.length > counter" @click="loadNextWord" class="solid">Next</button>
    </div>
    <div class="options">
      <div class="listen" @click="listenToWord" v-if="eng_voices">
        <i class="material-icons">record_voice_over</i>
      </div>
      <div class="speak" @click="speechRecognition">
        <i class="material-icons">hearing</i>
      </div>
    </div>
    <div class="options" v-if="eng_voices">
      <i class="material-icons" v-if="volume==0">volume_off</i>
      <i class="material-icons" v-if="volume>0">volume_down</i>
      <range-slider class="slider volume" min="0" max="100" step="5" v-model="volume"></range-slider>
      <i class="material-icons">volume_up</i>
    </div>
  </section>
</template>

<script>
let synth = window.speechSynthesis;
const awaitVoices = new Promise(
  done => (window.speechSynthesis.onvoiceschanged = done)
);
import RangeSlider from "vue-range-slider";
import "vue-range-slider/dist/vue-range-slider.css";
var SpeechRecognition =
  window.SpeechRecognition || window.webkitSpeechRecognition;
var SpeechGrammarList =
  window.SpeechGrammarList || window.webkitSpeechGrammarList;
export default {
  components: {
    RangeSlider
  },
  data() {
    return {
      forms: ["capital", "upper", "lower"],
      transform: "upper",
      words: ["bag"],
      counter: 0,
      voices: [],
      eng_voices: [],
      language: 8,
      volume: 50
    };
  },
  mounted() {
    this.loadAllWolds();
    this.listEnglishVoices();
    this.updateVolumeSlide();
  },
  methods: {
    loadNextWord() {
      while (this.words.length > this.counter) {
        this.counter += 1;
        break;
      }
    },
    loadLastWord() {
      while (this.counter > 0) {
        this.counter -= 1;
        break;
      }
    },
    loadAllWolds() {
      let $vm = this;
      fetch("../words.txt")
        .then(response => response.text())
        .then(data => {
          let words = data.split("\n");
          for (var i = 0; i < words.length; ++i) {
            $vm.checkCorrectWord(words[i].trim());
          }
        });
      $vm.words = $vm.words.sort();
    },
    checkCorrectWord(word) {
      let vowels = ["a", "e", "i", "o", "u"];
      if (
        !(
          vowels.includes(word.slice(0, 1)) || vowels.includes(word.slice(2, 3))
        ) &&
        vowels.includes(word.slice(1, 2))
      ) {
        this.words.push(word);
      }
    },
    listenToWord() {
      let $vm = this;
      var utterThis = new window.SpeechSynthesisUtterance(
        $vm.words[$vm.counter]
      );
      utterThis.voice = $vm.eng_voices[$vm.language];
      utterThis.volume = $vm.volume / 100;
      synth.speak(utterThis);
    },
    updateVolumeSlide() {
      var x = document.getElementsByClassName("range-slider-fill");
      var i;
      for (i = 0; i < x.length; i++) {
        x[i].style.backgroundColor = "teal";
      }
    },
    listEnglishVoices() {
      let $vm = this;
      awaitVoices.then(() => {
        $vm.voices = speechSynthesis.getVoices();
        let voicesLength = this.voices.length;
        for (let index = 0; index < voicesLength; index++) {
          const element = $vm.voices[index];
          if (element.lang.slice(0, 2) == "en") {
            $vm.eng_voices.push(element);
          }
        }
      });
    },
    speechRecognition() {
      var recognition = new SpeechRecognition();
      var speechRecognitionList = new SpeechGrammarList();
      var grammar =
        "#JSGF V1.0; grammar words; public <word> = " +
        this.words.join(" | ") +
        " ;";

      speechRecognitionList.addFromString(grammar, 1);
      SpeechRecognition.continuous = false;
      recognition.maxAlternatives = 1;

      recognition.start();

      recognition.onresult = function(event) {
        // var color = event.results[0][0].transcript;
        // diagnostic.textContent = "Result received: " + color + ".";
        // bg.style.backgroundColor = color;
        console.log(event.results[0][0]);
      };

      recognition.onspeechend = function() {
        recognition.stop();
      };
    }
  }
};
</script>

<style lang="less" scoped>
section {
  width: 100vw;
  height: calc(~"100vh - 80px");
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
section > * {
  max-width: 800px;
  min-width: 310px;
}
.standard {
  height: 36px;
  margin: 5px;
}
select {
  .standard();
  width: 100px;
  border-bottom: 1px solid teal;
  font-size: 11px;
  font-weight: bold;
  border-radius: 0px !important;
}
button {
  .standard();
  border-radius: 5px;
  min-width: 100px;
  border: 1px solid teal;
  font-size: 0.8rem;
  font-weight: bold;
  background-color: transparent;
}
.solid {
  background-color: teal;
  color: #fff;
}
.outline {
  background-color: white;
}
.clear {
  border: none;
  background-color: transparent;
}

.upper {
  text-transform: uppercase;
}

.lower {
  text-transform: lowercase;
}

.capital {
  text-transform: capitalize;
}
.words {
  font-size: 6rem;
}
.actions {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  padding: 5px;
  flex-wrap: nowrap;
  padding: 2rem 0;
}
.options {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  padding: 5px;
  flex-wrap: nowrap;
  margin: 10px 0;
}

.center {
  display: flex;
  align-items: center;
  justify-content: center;
}
.listen,
.speak {
  height: 60px;
  width: 60px;
  border: 1px solid teal;
  margin: 5px;
  border-radius: 5px;
  .center();
}

.listen:hover,
.speak:hover {
  transform: scale(1.2);
}
.setting-lang,
.setting-case {
  display: flex;
  flex-direction: column;
  text-align: left;
}
.settings {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  border-bottom: 1px solid teal;
}
label {
  padding: 5px 15px;
  font-size: 11px;
  font-weight: bold;
}
.volume {
  width: 100%;
}
.range-slider-fill {
  background-color: teal !important;
}
</style>