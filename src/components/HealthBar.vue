<template>
    <v-container>
        <v-row v-if="winner === 'draw'">
            <v-col cols="12" class="d-flex justify-center">
                <h2 class="win align-center">DRAW</h2>
            </v-col>
        </v-row>
        <v-row>
            <v-col cols="12" sm="6">
                <div class="d-flex justify-center">
              <span class="player" :class="{win: winner === 'player'}">Player
              <v-icon color="black">fas fa-male</v-icon>
                  ({{ player }})
              </span>

                </div>

                <v-progress-linear
                        color="light-blue"
                        height="100"
                        :value="player"
                        striped
                ></v-progress-linear>
                <div class="d-flex justify-start mt-1">
                    <v-icon :key="index" v-for="(life,index) in playerLivesArr"
                            class="heart"
                            :color="life ? heartColor : brokenHeartColor"
                            v-text="life ? normalHeart : brokenHeart"></v-icon>
                </div>
            </v-col>
            <v-col cols="12" sm="6">
                <div class="d-flex justify-center">
              <span class="player" :class="{win: winner === 'monster'}">Monster
              <v-icon color="black">fas fa-pastafarianism</v-icon>
                  ({{ monster }})
              </span>
                </div>
                <v-progress-linear
                        color="green"
                        height="100"
                        :value="monster"
                        striped
                ></v-progress-linear>
                <div class="d-flex justify-end mt-1">
                    <v-icon :key="index" v-for="(life,index) in monsterLivesArr"
                            class="heart"
                            :color="life ? heartColor : brokenHeartColor"
                            v-text="life ? normalHeart : brokenHeart"></v-icon>
                </div>
            </v-col>
        </v-row>
    </v-container>
</template>

<script>
    export default {
        name: "HealthBar",
        props:{
            player:{
                require:true,
                type: Number
            },
            monster:{
                require:true,
                type: Number
            },
            playerLives: {
                require: true,
                type: Number
            },
            monsterLives: {
                require: true,
                type: Number
            },
            gameStart: {
                require: true,
                type: Boolean
            },
            winner: {
                require: true
            }
        },

        watch: {
            monster: function (health) {
                if ( health <= 0 ) {
                    this.deathEvent("monster")
                }
            },
            player: function (health) {
                if ( health <= 0 ) {
                    this.deathEvent("player")
                }
            },
            playerLives: function () {
                this.addDeath("player");
            },
            monsterLives: function () {
                this.addDeath("monster")
            },
            gameStart: function (val) {
                if (val === true) {
                    this.initiate();
                }
            },
        },
        data: ()=>({
            heartColor: "#fb4e52",
            brokenHeartColor: "#222222",
            normalHeart: "fas fa-heart",
            brokenHeart: "fas fa-heart-broken",
            playerLivesArr: [],
            monsterLivesArr: [],
        }),

        methods: {
            initiate: function() {
                this.playerLivesArr = [];
                this.monsterLivesArr = [];
                for ( let i = 0; i < this.playerLives; i++ ) {
                    this.playerLivesArr.push(1)
                }

                for ( let i = 0; i < this.monsterLives; i++ ) {
                    this.monsterLivesArr.push(1)
                }
            },

            deathEvent: function (who) {
                this.$emit('death',who);
            },

            addDeath: function (who) {
                if (who === "player") {
                    this.playerLivesArr.shift()
                    this.playerLivesArr.push(0)
                }

                if (who === "monster") {
                    this.monsterLivesArr.pop()
                    this.monsterLivesArr.unshift(0)
                }




            },
        }
    }
</script>

<style scoped>
    .player {
        font-size: 26px;
        margin-bottom: 10px;
    }
    .heart {
        margin-right: 6px;
    }
    .win {
        background-color: #fb4e52;
        color: white;
        padding-right: 10px;
        padding-left: 10px;
        border-radius: 5px;
    }
</style>