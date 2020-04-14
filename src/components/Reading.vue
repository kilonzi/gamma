<template>
  <section>
    <div></div>
    <div>
      <div>
        <select name="transform" id="transform" class="outline" v-model="transform">
          <option value="upper">UPPER</option>
          <option value="lower">lower</option>
          <option value="capital">Capital</option>
        </select>
      </div>
      <div :class="transform" class="words">{{words[counter]}}</div>
    </div>
    <div class="options">
      <button v-if="counter>0" @click="loadLastWord" class="outline">Last</button>
      <button v-if="words.length > counter" @click="loadNextWord" class="solid">Next</button>
    </div>
  </section>
</template>

<script>
import txt from '../../words.txt';
console.log(txt)
export default {
  data() {
    return {
      forms: ["capital", "upper", "lower"],
      transform: "upper",
      words: ["Bag", "cat"],
      counter: 0
    };
  },
  mounted() {
    this.loadAllWolds();
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
        
    //   let file = "../../words.txt";
 
    }

    // getDancers() {
    //   var names = read("dancers.txt").split("\n");
    //   for (var i = 0; i < names.length; ++i) {
    //     names[i] = names[i].trim();
    //   }
    //   for (var i = 0; i < names.length; ++i) {
    //     var dancer = names[i].split(" ");
    //     var sex = dancer[0];
    //     var name = dancer[1];
    //     if (sex == "F") {
    //       females.enqueue(new Dancer(name, sex));
    //     } else {
    //       males.enqueue(new Dancer(name, sex));
    //     }
    //   }
    // }
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
}
.standard {
  height: 36px;
  margin: 5px;
  border: none;
}
select {
  .standard();
  min-width: 200px;
  border-bottom: 1px solid teal;
}
button {
  .standard();
  border-radius: 5px;
  min-width: 100px;
  border: 1px solid teal;
  font-size: 0.8rem;
  font-weight: bold;
}
.solid {
  background-color: teal;
  color: #fff;
}
.outline {
  background-color: white;
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
  font-size: 8rem;
}
.options {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  padding: 5px;
}
</style>