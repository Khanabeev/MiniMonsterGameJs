<template>
  <v-app>
    <v-app-bar
      app
      color="red"
    >
      <div class="d-flex align-center">
        <h3 class="white--text mr-4">Mini Monster</h3>
        <v-icon>fas fa-pastafarianism</v-icon>
        <v-icon>fas fa-pastafarianism</v-icon>
        <v-icon>fas fa-pastafarianism</v-icon>
      </div>

      <v-spacer></v-spacer>

    </v-app-bar>

    <v-content>
      <health-bar
              :player="playerHealth"
              :monster="monsterHealth"
              :player-lives="playerLives"
              :monster-lives="monsterLives"
              @death="logDeath"
              :game-start="isGameRunning"
              :winner="winner"
      ></health-bar>
      <v-container>
        <v-row>
          <v-col v-if="isGameRunning || isGameFinish" class="d-flex justify-center">
              <v-btn class="btn mr-4 red" :disabled="isGameFinish" @click="attack(0)">Attack</v-btn>
              <v-btn class="btn mr-4 warning" :disabled="isGameFinish" @click="attack(10)">Super Attack</v-btn>
              <v-btn class="btn mr-4 primary" :disabled="isGameFinish" @click="heal(10)">Heal</v-btn>
              <v-btn class="btn grey white--text" :disabled="isGameFinish" @click="giveUp">Give up</v-btn>
          </v-col>
          <v-col v-else class="d-flex justify-center">
            <v-btn class="btn primary white--text" @click="startGame">Start
            <v-icon class="ml-3">fab fa-steam-symbol</v-icon>
            </v-btn>
          </v-col>
          <v-col cols="12" v-if="isGameFinish" class="d-flex justify-center">
            <v-btn class="btn primary white--text" @click="startGame">Play again
              <v-icon class="ml-3">fas fa-redo</v-icon>
            </v-btn>
          </v-col>
        </v-row>

        <v-row>
          <v-col offset-lg="3" offset-md="3"  lg="6" md="6" sm="12">
            <v-alert :type="log.who === 'player' ? 'info' : 'warning'" :key="index" v-for="(log,index) in battleLog">
              {{ log.text }}
            </v-alert>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>

import HealthBar from "./components/HealthBar";
export default {
  name: 'App',
  components: {HealthBar},
  data: () => ({
    monsterHealth: 0,
    playerHealth: 0,

    playerLives: 0,
    monsterLives: 0,

    playerPower: 10,
    monsterPower: 10,

    isGameRunning: false,
    isGameFinish: false,

    winner: undefined,

    battleLog: [],
  }),
  methods: {

    startGame: function () {
      this.isGameRunning = true;
      this.isGameFinish = false;
      this.monsterHealth = 100;
      this.playerHealth = 100;
      this.playerLives = 3;
      this.monsterLives = 3;
      this.winner = undefined;
      this.battleLog = [];
    },

    giveUp: function () {
      this.isGameRunning = false;
      this.isGameFinish = false;
      this.monsterHealth = 100;
      this.playerHealth = 100;
      this.playerLives = 3;
      this.monsterLives = 3;
      this.winner = undefined;
      this.battleLog = [];
    },

    logDeath: function (who) {
      switch (who) {
        case "player":
          this.playerLives -= 1;
          this.isDeath("player")
          break
        case "monster":
          this.monsterLives -= 1;
          this.isDeath("monster")
          break
      }
    },

    attack: function ( x ) {
      let monsterAttack = this.randomNum(this.monsterPower + x );
      let playerAttack = this.randomNum(this.playerPower + x );
      this.addToBattleLog("Monster hit: " + monsterAttack, "monster");
      this.addToBattleLog("Player hit: " + playerAttack, "player");
      this.playerHealth -= monsterAttack;
      this.monsterHealth -= playerAttack;
    },
    heal: function ( x ) {
      let monsterHeal = this.randomNum(this.monsterPower + x );
      let playerHeal =  this.randomNum(this.playerPower + x );
      this.addToBattleLog("Monster heal: " + monsterHeal, "monster");
      this.addToBattleLog("Player heal: " + playerHeal, "player");

      if ( (this.playerHealth + playerHeal) >= 100 ) {
        this.playerHealth = 100;
      } else {
        this.playerHealth += playerHeal;
      }

      if ( (this.monsterHealth + monsterHeal) >= 100 ) {
        this.monsterHealth = 100;
      } else {
        this.monsterHealth += monsterHeal;
      }
    },
    randomNum: function ( x ) {
      return Math.floor( Math.random() * x );
    },
    isAlive: function (who) {
      switch (who) {
        case "player":
          return this.playerLives > 0;
        case "monster":
          return this.monsterLives > 0;
      }
    },
    isDeath: function (who) {
      if ( !this.isAlive(who) ) {
        this.stopGame()
      } else {
        this.nextLife(who)
      }
    },

    nextLife: function (who) {
      switch (who) {
        case "player":
          this.playerHealth = 100;
          break;
        case "monster":
          this.monsterHealth = 100;
          break;
      }
    },

    addToBattleLog: function (text,who) {
      this.battleLog.unshift({
        who: who,
        text: text,
      });
    },

    stopGame: function () {
      this.isGameFinish = true;
      this.isGameRunning = false;
      if (this.playerLives > this.monsterLives) {
        this.winner = "player"
      } else if (this.playerLives < this.monsterLives) {
        this.winner = "monster"
      } else {
        this.winner = "draw"
      }
    },

  }
};
</script>

<style scoped>
  .box {
    width: auto;
    height: 100px;
  }
  .shadow {
    box-shadow: grey 1px 1px 5px;
  }
</style>
