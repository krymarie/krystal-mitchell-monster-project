<template class="ma-auto">
  <v-container class="ma-auto">
    <v-layout
      text-center
      wrap
      class="ma-auto"
    >
        <v-flex xs12>
        <section class="ma-2 mx-auto" class="row">
        <div class="small-6 columns">
            <v-img class="text-center mb-4 mx-auto" max-width="150" src="assets/guy.png"></v-img>
            <div class="healthbar">
                <div 
                class="healthbar text-center fill-height repeating-gradient"
                style="background-color: indigo; margin: 0; color: white;"
                :style= "{width: playerHealth + '%'}">
                {{playerHealth}}
                </div>
            </div>
        </div>
        <div class="small-6 columns">
            <v-img class="text-center mb-4 mx-auto" max-width="151" src="assets/zombieGuy.png"></v-img>
            <h1></h1>
            <div class="healthbar">
                <div 
                class="healthbar text-center fill-height repeating-gradient" 
                style="background-color: red; margin: 0; color: white;"
                :style= "{width: monsterHealth + '%'}">
                {{monsterHealth}}
                </div>
            </div>
        </div>
    </section>
    <section class="row controls" v-if="!gameIsRunning">
        <div class="small-12 columns">
            <v-btn id="start-game" @click="startGame" outlined color="indigo">START NEW GAME</v-btn>
        </div>
    </section>
    <section class="row controls" v-else>
        <div class="small-12 columns">
            <v-btn id="attack" @click="attack" outlined color="red">New ATTACK</v-btn>
            <v-btn id="special-attack" @click="specialAttack" outlined color="orange">SPECIAL ATTACK</v-btn>
            <v-btn id="heal" @click="heal" outlined color="green">HEAL</v-btn>
            <v-btn id="give-up" @click="giveUp" outlined color="dark">GIVE UP</v-btn>
        </div>
    </section>
    
    <section class="ma-2 ma-auto" class="row">
      
      <section class="row log ma-auto" v-if="turns.length > 0">
          <div class="columns ma-auto">
              <ul class="ma-auto" >
                  <li v-for="turn in turns" v-show="turn.isPlayer"
                      :class="{'player-turn': turn.isPlayer}">
                    {{ turn.text }}
                  </li>
              </ul>
          </div>
      </section>

      <section class="row log ma-auto" v-if="turns.length > 0">
          <div class="columns ma-auto">
              <ul class="ma-auto" >
                  <li v-for="turn in turns" v-show="!turn.isPlayer"
                      :class="{'monster-turn': !turn.isPlayer}">
                    {{ turn.text }}
                  </li>
              </ul>
          </div>
      </section>

    </section>
    
    </v-layout>
  </v-container>
</template>

<script>
export default {

  data: () => ({
    showModal: false,
    playerHealth: 100,
    monsterHealth: 100,
    gameIsRunning: false,
    turns: []
  }),
  methods: {
    startGame: function() {
      this.gameIsRunning = true;
      this.playerHealth = 100;
      this.monsterHealth = 100;
      this.turns = [];
    },
    attack: function() {
      var damage = this.calculateDamage(3, 10);
      this.monsterHealth -= damage;
      this.turns.unshift({
        isPlayer: true,
        text: 'Player hits Monster with ' + damage
      });
      if(this.checkWin()) {
        return;
      }
      this.monsterAttacks();
    },

    specialAttack: function() {
      var damage = this.calculateDamage(10, 20);
      this.monsterHealth -= damage;
      this.turns.unshift({
        isPlayer: true,
        text: 'Player hits Monster hard ' + damage
      });
      if(this.checkWin()) {
        return;
      }
      this.monsterAttacks();
    },

    heal: function() {
      if (this.playerHealth <= 90) {
        this.playerHealth += 10;
      } else {
        this.playerHealth = 100;
      }
      this.turns.unshift({
        isPlayer: true,
        text: 'Player heals by 10 '
      });
      this.monsterAttacks();      
    },

    giveUp: function() {
      this.gameIsRunning = false;
    },
    monsterAttacks: function() {
      var damage = this.calculateDamage(5, 12);
      this.playerHealth -= damage;
      this.checkWin();
      this.turns.unshift({
        isPlayer: false,
        text: 'Monster hits Player with ' + damage
      });
    },
    calculateDamage: function(min, max) {
      return Math.max(Math.floor(Math.random() * max) +1, min);
    },
   
    checkWin: function() {
      if (this.monsterHealth <= 0) {
        if (confirm('Your won! New Game?')) {
          this.startGame();
        } else {
          this.gameIsRunning = false;
        }
        return true;
      } else if (this.playerHealth <= 0) {
        if (confirm('Your lost! New Game?')) {
          this.startGame();
        } else {
          this.gameIsRunning = false;
        }
        return true;
      }
      return false;
    }
  }

};
</script>
