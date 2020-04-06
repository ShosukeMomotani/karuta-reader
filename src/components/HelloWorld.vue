<template>
  <div class="hello">
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
        <input class="form-control" type="number" v-model.number="duration" />
      </div>
    </form>
    <p>{{ selected }}</p>
    <ul>
      <li v-for="item in cards" :key="item">{{ item }}</li>
    </ul>
  </div>
</template>

<script>
const _getRandom = (min, max) => {
  return min + Math.random() * (max - min);
};

export default {
  name: "HelloWorld",
  props: {},
  data: function() {
    return {
      cards: require("../data/cards.json"),
      selected: "",
      autoplay: false,
      duration: 5
    };
  },
  methods: {
    read: function() {
      if (this.cards.length === 0) return;

      // shuffle
      (array => {
        for (var i = array.length - 1; i > 0; i--) {
          var r = Math.floor(Math.random() * (i + 1));
          var tmp = array[i];
          array[i] = array[r];
          array[r] = tmp;
        }
      })(this.cards);

      // pickup
      this.selected = this.cards.pop();

      // speech
      const utter = new SpeechSynthesisUtterance(this.selected);
      utter.onend = () => {
        console.log(this.selected);
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
