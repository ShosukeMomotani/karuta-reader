<template>
  <div class="hello">
    <div class="container">
      <form class="form-inline justify-content-center">
        <div class="col-auto">
          <button class="btn btn-primary" type="button" v-on:click="read()">スタート</button>
        </div>
        <div class="col-auto">
          <div class="form-check mb-2">
            <input class="form-check-input" type="checkbox" id="check-autoplay" v-model="autoplay" />
            <label class="form-check-label" for="check-autoplay">Auto Play</label>
          </div>
        </div>
        <div class="col-auto">
          <input
            class="form-control"
            type="number"
            id="num-duration"
            placeholder="Duration"
            v-model.number="duration"
          />
        </div>
      </form>
    </div>
    <div class="container">
      <p class="h4">{{ selected }}</p>
    </div>
    <div class="container">
      <ul class="list-group">
        <li class="list-group-item" v-for="item in cards" :key="item">{{ item }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
const _getRandom = (min, max) => {
  return min + Math.random() * (max - min);
};

const _checkOdds = odds => {
  return Math.random() <= odds;
};

export default {
  name: "HelloWorld",
  props: {},
  data: function() {
    return {
      cards: require("../data/cards.json"),
      selected: "",
      autoplay: false,
      duration: 5,
      trashes: []
    };
  },
  methods: {
    read: function() {
      if (this.cards.length === 0) return;

      // trap
      const cards =
        this.trashes.length > 0 && _checkOdds(0.05) ? this.trashes : this.cards;

      // shuffle
      (array => {
        for (var i = array.length - 1; i > 0; i--) {
          var r = Math.floor(Math.random() * (i + 1));
          var tmp = array[i];
          array[i] = array[r];
          array[r] = tmp;
        }
      })(cards);

      // pickup
      const selected = cards.pop();

      // speech
      const utter = new SpeechSynthesisUtterance(selected);
      utter.onend = () => {
        this.selected = selected;
        this.trashes.push(selected);

        // autoplay
        if (this.autoplay && this.cards.length > 0)
          setTimeout(() => {
            if (this.autoplay) this.read();
          }, _getRandom((this.duration - 1) * 1000, (this.duration + 1) * 1000));
      };
      utter.rate = _getRandom(0.5, 2);
      utter.pitch = _getRandom(0.5, 2);
      utter.volume = _getRandom(0.5, 1);
      speechSynthesis.speak(utter);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
