<template>
  <div class="hello">
    <button v-on:click="read()">スタート</button>
    <input type="checkbox" v-model="autoplay" />
    <input type="number" v-model.number="duration" />

    <p>{{ selected }}</p>
    <ul>
      <li v-for="item in cards" :key="item">{{ item }}</li>
    </ul>
  </div>
</template>

<script>
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
          }, (this.duration - 1) * 1000 + Math.random() * 2000);
      };
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
