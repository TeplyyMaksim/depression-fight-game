<template>
  <div id="app">
    <h1>Fight or not, you'll loose!</h1>
    <div class="header">
      <div class="side">
        <h2>You</h2>
        <img src="./assets/you.png" alt="you">
        <div class="health" :style="calculateHealthBar(playerHealth, playerInitialHealth)">
          {{playerHealth}}/{{playerInitialHealth}}
        </div>
      </div>
      <div class="vs">
        <img src="./assets/vs.png" alt="versus sign">
      </div>
      <div class="side">
        <h2>Depression</h2>
        <img src="./assets/depression.png" alt="depression">
        <div class="health" :style="calculateHealthBar(depressionHealth, depressionInitialHealth)">
          {{depressionHealth}}/{{depressionInitialHealth}}
        </div>
      </div>
    </div>

    <div class="control">
      <button v-if="!gameGoing" type="button" @click="gameGoing = true">Start new game</button>

      <template v-if="gameGoing">
        <button type="button" @click="gameGoing = false">Surrender</button>
        <button type="button" @click="makeDmg()">Attack</button>
        <button type="button" @click="makeDmg({ superAttack: 1.5 })">Supper attack</button>
      </template>
    </div>

    <ul class="fight-log">
      <li
        v-for="action in actionList"
        :class="[
          'log',
          { 'dep-attack': action.side === 'depression' },
          { 'your-attack': action.side === 'player'}]
        "
      >
        {{action.side}} atacked on {{action.dmg}} health</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'app',

  data() {
    return {
      gameGoing: false,

      playerHealth: 100,
      depressionHealth: 110,
      playerInitialHealth: 100,
      depressionInitialHealth: 110,

      actionList: [],
    };
  },
  methods: {
    calculateHealthBar(currentHealth, totalHealth) {
      return { backgroundSize: `${Math.floor((currentHealth * 100) / totalHealth)}% 100%` };
    },
    getNumDmg(min = 1, max = 11) {
      return Math.floor(Math.random() * (max - min)) + min;
    },
    attackDepression(superAttack) {
      const yourAttack = this.getNumDmg();
      const depAttack = this.getNumDmg();

      this.depressionHealth -= yourAttack * superAttack;
      this.actionList.push({ side: 'player', dmg: yourAttack });
      this.playerHealth -= depAttack;
      this.actionList.push({ side: 'depression', dmg: depAttack });
    },
    makeDmg({ superAttack } = { superAttack: 1 }) {
      this.attackDepression(superAttack);
      if (this.depressionHealth <= 0 && this.playerHealth <= 0) {
        alert('You and your depression fade away!');
        this.gameGoing = false;
      } else if (this.depressionHealth <= 0) {
        alert('You win. Now you should be happy!');
        this.gameGoing = false;
      } else if (this.playerHealth <= 0) {
        alert('Depression wins. Try to beat it later!');
        this.gameGoing = false;
      }
    },
  },
  watch: {
    gameGoing(value) {
      if (value === true) {
        this.playerHealth = this.playerInitialHealth;
        this.depressionHealth = this.depressionInitialHealth;
        this.actionList = [];
      }
    },
  },
};
</script>

<style>
*{
  box-sizing: border-box;
}


#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 10px;
}


.header {
  display: flex;
  justify-content: center;
  align-items: center;
}
.header .vs img {
  width: 150px;
  height: auto;
}
.header .side {
  width: 200px;
}
.header .side img {
  width: 100%;
  height: auto;
}
.header .side .health {
  width: 200px;
  height: 30px;
  background: url('./assets/greenhealth.png') no-repeat;
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px solid darkgreen;
}


.control {
  width: 550px;
  margin: 10px auto 0;
  height: 100px;
  background: lightgrey;
  border: 1px solid black;
  display: flex;
  justify-content: center;
  align-items: center;
}
.control button {
  width: 100px;
  margin: 10px;
  height: 80px;
}


.fight-log {
  width: 550px;
  margin: 10px auto;
  background: lightgrey;
  border: 1px solid black;
  list-style: none;
  padding: 0;
}
.fight-log .log {
  margin: 5px;
  padding: 5px;
  text-transform: capitalize;
}
.fight-log .log.your-attack {
  background: lightgreen;
}
.fight-log .log.dep-attack {
  background: tomato;
}
</style>
