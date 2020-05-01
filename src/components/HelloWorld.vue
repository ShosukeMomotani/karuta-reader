<template>
  <div class="hello">
    <div class="container">
      <form class="form-horizontal">
        <div class="form-group">
          <div class="col-xs-offset-2 col-xs-10">
            <div class="form-check form-check-inline">
              <input
                class="form-check-input"
                type="checkbox"
                id="radio-pokemon"
                value="pokemon"
                v-model="cardsetPokemon"
              />
              <label class="form-check-label" for="radio-pokemon">ポケモン</label>
            </div>
            <div class="form-check form-check-inline">
              <input
                class="form-check-input"
                type="checkbox"
                id="radio-nazonazo"
                value="nazonazo"
                v-model="cardsetNazonazo"
              />
              <label class="form-check-label" for="radio-nazonazo">なぞなぞ</label>
            </div>
          </div>
        </div>
        <div class="form-group row">
          <label for="num-duration" class="col-sm-2 col-form-label">Duration</label>
          <div class="col-sm-10">
            <input class="form-control" type="number" id="num-duration" v-model.number="duration" />
          </div>
        </div>
        <div class="form-group row">
          <label for="num-trap-rate" class="col-sm-2 col-form-label">Trap Rate</label>
          <div class="col-sm-10">
            <input class="form-control" type="number" id="num-trap-rate" v-model.number="trapRate" />
          </div>
        </div>
        <div class="form-group row">
          <label for="num-jump-in-rate" class="col-sm-2 col-form-label">Jump In Rate</label>
          <div class="col-sm-10">
            <input
              class="form-control"
              type="number"
              id="num-jump-in-rate"
              v-model.number="jumpInRate"
            />
          </div>
        </div>
        <div class="form-group">
          <div class="col-xs-offset-2 col-xs-10">
            <input class="form-check-input" type="checkbox" id="check-autoplay" v-model="autoplay" />
            <label class="form-check-label" for="check-autoplay">Auto Play</label>
          </div>
        </div>
        <div class="form-group">
          <button class="btn btn-primary" type="button" v-on:click="read()">スタート</button>
        </div>
      </form>
    </div>
    <div class="container">
      <p class="h4">{{ selected }}</p>
    </div>
    <div class="container">
      <ul class="list-group">
        <li class="list-group-item" v-for="(item, index) in cards" :key="index">{{ item.join(" ") }}</li>
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

const cardSets = {
  pokemon: require("../data/cards_pokemon.json"),
  nazonazo: require("../data/cards_nazonazo.json")
};

export default {
  name: "HelloWorld",
  props: {},
  data: function() {
    return {
      cardsetPokemon: true,
      cardsetNazonazo: false,
      cards: cardSets["pokemon"].slice(),
      selected: "",
      autoplay: false,
      duration: 5,
      trapRate: 5,
      jumpInRate: 10,
      trashes: []
    };
  },
  watch: {
    cardsetPokemon: function() {
      this.cards = [];
      if (this.cardsetPokemon)
        this.cards = this.cards.concat(cardSets.pokemon.slice());
      if (this.cardsetNazonazo)
        this.cards = this.cards.concat(cardSets.nazonazo.slice());
      this.trashes = [];
    },
    cardsetNazonazo: function() {
      this.cards = [];
      if (this.cardsetPokemon)
        this.cards = this.cards.concat(cardSets.pokemon.slice());
      if (this.cardsetNazonazo)
        this.cards = this.cards.concat(cardSets.nazonazo.slice());
      this.trashes = [];
    }
  },
  methods: {
    read: function() {
      if (this.cards.length === 0) return;

      // trap
      const cards =
        this.trashes.length > 0 && _checkOdds(this.trapRate / 100)
          ? this.trashes
          : this.cards;

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
      const text = (_checkOdds(this.jumpInRate / 100)
        ? selected.slice(1)
        : selected
      ).join("、");
      const utter = new SpeechSynthesisUtterance(text);
      utter.onend = () => {
        this.selected = text;
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
