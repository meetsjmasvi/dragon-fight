<template>
  <b-container fluid id="app" class="h-100">
    <b-row class="h-100" align-v="center">
      <b-col cols="12" v-if="!gameIsRunning">
        <b-row>
          <b-col>
            <b-jumbotron
              header="𝕯𝖗𝖆𝖌𝖔𝖓 𝕱𝖎𝖌𝖍𝖙"
              lead="Product of Media Corp"
              class="controls"
              vertical-align="center"
            >
              <b-button
                variant="secondary"
                id="start-game"
                v-shortkey="['enter']"
                @shortkey="startGame"
                @click="startGame"
              >Start Game</b-button>
              <b-tooltip target="start-game" placement="bottom" title="Shortcut Key: Enter"></b-tooltip>
            </b-jumbotron>
          </b-col>
        </b-row>
      </b-col>
      <b-col cols="12" v-else>
        <b-row>
          <b-col cols="6" class="human">
            <div>
              <img
                id="human"
                :class="{hide: humanLost, lost: humanLost}"
                alt="Commentry"
                height="250"
                src="../assets/he-man.png"
              />
            </div>
            <div class="health-bar">
              <div class="text-center" :style="{width:playerHealth + '%'}"></div>
              <span class="health-level">{{playerHealth}}</span>
            </div>
          </b-col>
          <b-col cols="6" class="dragon">
            <div>
              <img
                id="dragon"
                :class="{hide: dragonLost, lost: dragonLost}"
                alt="Commentry"
                height="250"
                src="../assets/dragon.png"
              />
            </div>
            <div class="health-bar">
              <div class="text-center" :style="{width:dragonHealth + '%'}"></div>
              <span class="health-level">{{dragonHealth}}</span>
            </div>
          </b-col>
        </b-row>
        <b-row>
          <b-col class="button-controls" offset="2" cols="8">
            <button id="attack" v-shortkey="['arrowup']" @shortkey="attack" @click="attack">ATTACK</button>
            <button
              id="blast"
              v-shortkey="['arrowdown']"
              @shortkey="blast"
              @click="blast"
            >SPECIAL ATTACK</button>
            <button id="heal" v-shortkey="['arrowleft']" @shortkey="heal" @click="heal">HEAL</button>
            <button
              id="give-up"
              v-shortkey="['arrowright']"
              @shortkey="giveUp"
              @click="giveUp"
            >GIVE UP</button>
            <b-tooltip target="attack" placement="bottom" title="Shortcut Key: Up Arrow"></b-tooltip>
            <b-tooltip target="blast" placement="bottom" title="Shortcut Key: Down Arrow"></b-tooltip>
            <b-tooltip target="heal" placement="bottom" title="Shortcut Key: Left Arrow"></b-tooltip>
            <b-tooltip target="give-up" placement="bottom" title="Shortcut Key: Right Arrow"></b-tooltip>
          </b-col>
        </b-row>
        <b-row class="commentry">
          <b-col offset="2" cols="8" offset-md="2" md="8" v-if="turns.length>0">
            <ul align-self="center">
              <li
                v-for="(turn, index) in turns"
                v-bind:key="index"
                align-h="center"
                :class="{'player-turn': turn.isPlayer, 'dragon-turn': !turn.isPlayer}"
              >{{turn.text}}</li>
            </ul>
          </b-col>
        </b-row>
      </b-col>
    </b-row>
    <b-modal
      modal-class="dragon-modal"
      ref="game-over"
      header-class="dragon-header"
      title="Game Over"
      @ok="handleOk"
    >
      <div class="d-block">{{gameResult}}</div>
    </b-modal>
  </b-container>
</template>

<script>
export default {
  data: function() {
    return {
      playerHealth: 100,
      dragonHealth: 100,
      humanLost: false,
      dragonLost: false,
      gameIsRunning: false,
      turns: [],
      gameResult: ""
    };
  },
  methods: {
    startGame: function() {
      this.gameIsRunning = true;
      this.playerHealth = 100;
      this.dragonHealth = 100;
      this.humanLost = false;
      this.dragonLost = false;
      this.turns = [];
      this.gameResult = "";
    },
    attack: function() {
      let damage = this.calculateDamage(3, 10);
      this.dragonHealth -= damage;
      this.turns.unshift({
        isPlayer: true,
        text: `You hit dragon for ${damage}`
      });

      if (this.checkWin()) {
        return;
      }

      this.dragonAttacks();
    },
    blast: function() {
      let damage = this.calculateDamage(8, 20);
      this.dragonHealth -= damage;
      this.turns.unshift({
        isPlayer: true,
        text: `You hit dragon HARD for ${damage}`
      });

      if (this.checkWin()) {
        return;
      }

      this.dragonAttacks();
    },
    heal: function() {
      if (this.playerHealth <= 90) {
        this.playerHealth += 10;
      } else {
        this.playerHealth = 100;
      }
      this.turns.unshift({
        isPlayer: true,
        text: "You heal for 10"
      });
      this.dragonAttacks();
    },
    giveUp: function() {
      this.gameIsRunning = false;
    },
    showModal: function() {
      this.$refs["game-over"].show();
    },
    handleOk: function() {
      this.startGame();
      this.gameIsRunning = false;
    },
    dragonAttacks: function() {
      let damage = this.calculateDamage(5, 12);
      this.playerHealth -= damage;
      this.checkWin();
      this.turns.unshift({
        isPlayer: false,
        text: `dragon hits you for ${damage}`
      });
    },
    calculateDamage: function(min, max) {
      return Math.max(Math.floor(Math.random() * max) + 1, min);
    },
    checkWin: function() {
      if (this.dragonHealth <= 0) {
        this.gameResult = "You Win! Do you want to re-match?";
        this.showModal();
        this.dragonLost = true;
        return true;
      } else if (this.playerHealth <= 0) {
        this.gameResult = "You lost! Do you want to re-match?";
        this.showModal();
        this.humanLost = true;
        return true;
      }
      return false;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* Jumbotron */
.jumbotron {
  background-color: transparent;
}

.jumbotron.controls {
  border: none;
  box-shadow: none;
  -webkit-box-shadow: none;
  font-family: "Franklin Gothic Medium", "Arial Narrow", Arial, sans-serif;
}

.jumbotron.controls h1 {
  color: rgb(255, 165, 0);
}

.jumbotron.controls p {
  color: rgba(181, 198, 208, 1);
}

/* Game Styling */
.health-bar {
  width: 80%;
  height: 40px;
  margin: auto;
  position: relative;
  transition: width 500ms;
  border: solid 5px #333;
}

.text-center {
  margin: 0px;
  height: 100%;
  background: linear-gradient(
    to bottom,
    rgba(254, 204, 177, 1) 0%,
    rgba(241, 116, 50, 1) 50%,
    rgba(234, 85, 7, 1) 51%,
    rgba(251, 149, 94, 1) 100%
  );
}

.human .text-center {
  background: linear-gradient(
    to bottom,
    rgba(191, 210, 85, 1) 0%,
    rgba(142, 185, 42, 1) 50%,
    rgba(114, 170, 0, 1) 51%,
    rgba(158, 203, 45, 1) 100%
  );
}

span.health-level {
  top: 50%;
  left: 50%;
  color: #fff;
  font-weight: 600;
  line-height: 30px;
  position: absolute;
  display: inline-block;
  transform: translate(-50%, -50%);
}

.commentry {
  max-height: 200px;
  min-height: 200px;
  overflow-y: auto;
}

.turn {
  margin-top: 20px;
  margin-bottom: 20px;
  font-weight: bold;
  font-size: 22px;
}

.commentry ul {
  list-style: none;
  font-weight: bold;
  text-transform: uppercase;
  padding-left: 0px;
}

.commentry ul li {
  margin: 5px;
  border-radius: 10px;
}

.commentry ul .player-turn {
  color: blue;
  background-color: rgba(191, 210, 85, 1);
}

.commentry ul .dragon-turn {
  color: red;
  background-color: rgba(254, 204, 177, 1);
}

.button-controls {
  padding-top: 50px;
}

.lost {
  animation: blind 2s 1;
}

.hide {
  opacity: 0;
}

/* animation */
@keyframes blind {
  0% {
    opacity: 1;
  }
  5% {
    opacity: 0;
  }
  10% {
    opacity: 1;
  }
  15% {
    opacity: 0;
  }
  20% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}

button {
  font-size: 18px;
  background-color: #eee;
  padding: 12px;
  box-shadow: 0 1px 1px black;
  margin: 10px;
  position: relative;
  overflow: hidden;
  width: 175px;
}

/* Shine */
button:after {
  top: 0;
  z-index: 1;
  content: "";
  width: 100%;
  height: 100%;
  position: absolute;
  transform: translateX(100%);
  animation: slide 2s infinite;

  background: linear-gradient(
    to right,
    rgba(255, 255, 255, 0) 0%,
    rgba(255, 255, 255, 0.8) 50%,
    rgba(128, 186, 232, 0) 99%,
    rgba(125, 185, 232, 0) 100%
  );
}

#start-game {
  position: relative;
  overflow: hidden;

  background: linear-gradient(
    to bottom,
    rgba(191, 210, 85, 1) 0%,
    rgba(142, 185, 42, 1) 50%,
    rgba(114, 170, 0, 1) 51%,
    rgba(158, 203, 45, 1) 100%
  );
}

#start-game:hover {
  font-weight: 600;
}

/* animation */
@keyframes slide {
  0% {
    transform: translateX(-200%) skew(-60deg, 0);
  }
  100% {
    transform: translateX(200%) skew(-60deg, 0);
  }
}

#start-game:hover {
  background-color: #76ff7e;
}

#attack {
  background: linear-gradient(
    to bottom,
    rgba(239, 197, 202, 1) 0%,
    rgba(210, 75, 90, 1) 50%,
    rgba(186, 39, 55, 1) 51%,
    rgba(241, 142, 153, 1) 100%
  );
}

#attack:hover {
  font-weight: 600;
  color: #fff;
}

#blast {
  background: linear-gradient(
    to bottom,
    rgba(254, 204, 177, 1) 0%,
    rgba(241, 116, 50, 1) 50%,
    rgba(234, 85, 7, 1) 51%,
    rgba(251, 149, 94, 1) 100%
  );
}

#blast:hover {
  font-weight: 600;
  color: #fff;
}

#heal {
  background: linear-gradient(
    to bottom,
    rgba(191, 210, 85, 1) 0%,
    rgba(142, 185, 42, 1) 50%,
    rgba(114, 170, 0, 1) 51%,
    rgba(158, 203, 45, 1) 100%
  );
}

#heal:hover {
  font-weight: 600;
  color: #fff;
}

#give-up {
  background: linear-gradient(
    to bottom,
    rgba(242, 246, 248, 1) 0%,
    rgba(216, 225, 231, 1) 50%,
    rgba(181, 198, 208, 1) 51%,
    rgba(224, 239, 249, 1) 100%
  );
}

#give-up:hover {
  font-weight: 600;
  color: #fff;
}

/* Modal Styles */

.dragon-modal .modal-dialog .modal-header,
.dragon-modal .modal-dialog .modal-footer {
  background: #292929;
}

.dragon-modal .modal-dialog .modal-title {
  color: #eaad04;
  background-color: #292929;
}

.dragon-modal .modal-dialog .modal-body {
  background-color: #333;
  color: #f2c79f;
}

.dragon-header {
  background-color: #333;
  color: #f2c79f;
}

@media screen and (max-width: 468) {
  .jumbotron.controls h1 {
    font-size: 9vw;
  }
}
</style>
