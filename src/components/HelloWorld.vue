<template>
  <v-container style="background:#84848496; width:100%; max-width: 100%; min-height: 100%;">
    <v-row style="height: 400px">
      <v-col cols="6">
        <v-card class="ma-5 pa-5" style="height: 100%">
          <v-row align="start" justify="center">
            <v-col>
              <v-slider
                v-model="players"
                min="3"
                max="100"
                :label="'Players - ' + players"
              ></v-slider>
            </v-col>  
          </v-row>

          <v-row align="start" justify="center">
            <v-col>
              <v-slider
                v-model="villagers"
                min="2"
                :max="players - werewolves"
                :label="'Villagers - ' + villagers"
              ></v-slider>
            </v-col>
          </v-row>

          <v-row align="center" justify="center">
            <v-col>
              <v-slider
                v-model="werewolves"
                min="1"
                :max="players - villagers"
                :label="'Werewolves - ' + werewolves"
              ></v-slider>
            </v-col>
          </v-row>

          <v-row align="center" justify="center">
            <v-col>
              <v-slider
                v-model="iterations"
                min="1"
                max="1000"
                :label="'Games - ' + iterations"
              ></v-slider>
            </v-col>
          </v-row>

          
        </v-card>        
      </v-col>
      <v-col cols="6">
        <v-card class="ma-5 ml-0 pa-5" style="height: 100%">
          <v-col>
            <v-row v-for="(message, m) in log" :key="m">
              {{ message }}
            </v-row>
          </v-col>
        </v-card>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="6">
        <v-card class="pa-5 ma-5">
          <v-row align="center" justify="center">
            <v-col>
              <v-btn v-on:click="clear">Clear All</v-btn>
            </v-col>
            <v-col>
              <v-btn v-on:click="initGame">Calculate</v-btn>
            </v-col>
          </v-row>       
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  export default {
    name: 'HelloWorld',

    data: () => ({
      players: 3,
      villagers: 2,
      werewolves: 1,
      iterations: 1,
      wins: {
          villagers: 0,
          werewolves: 0
      },
      roles: [],
      persons: [],
      log: [],
      end: true
    }),
    methods: {
      reset: function() {
        this.roles = []
        this.persons = []
        this.log = []
      },
      clear: function(){
        this.reset()
        this.iterations = 1
        this.wins = {
          werewolves: 0,
          villagers: 0
        }
      },
      fillRoles: function() {
        for(let v=0;v<this.villagers;v++)this.roles.push('villager')
        for(let w=0;w<this.werewolves;w++)this.roles.push('werewolf')
      },
      getRole: function() {
        let role = Math.floor(Math.random() * this.roles.length); // 0 - players
        role = this.roles.splice(role, 1)[0]
        return role
      },
      createPlayer: function(id) {
        return {
          alive: true,
          id: id,
          role: this.getRole()
        }
      },
      killBy: function( killer ) {
        let targets = this.persons.filter((p)=>{
          return p.role != killer
        })
        let kill = Math.floor(Math.random() * targets.length)
        let killed = targets[kill]
        this.persons = this.persons.filter((k)=>{
          return k.id != killed.id
        })
        this.log.push(killed.role + ' was killed by ' + killer)
      },
      checkGameEnd: function () {
          let targets = this.persons.filter((p)=>{
            return p.role != 'werewolf'
          })
          if(targets.length == 0){
              this.log.push("the werewolves have won!")
              this.end = true
              return true
          }

          let werewolves = this.persons.filter((p)=>{
            return p.role != 'villager'
          })
          if(werewolves.length == 0){
            this.log.push("the villagers have won!")
            this.end = true
            return true
          }

          return false
      },
      initGame: function(){
        this.wins = {
          werewolves: 0,
          villagers: 0
        }
        for(let i=0;i<this.iterations;i++){
            this.loop()
        }
        this.log.push("werewolves: " + this.wins.werewolves)
        this.log.push("villagers: " + this.wins.villagers)
      },
      loop: function() {
        this.reset()
        this.fillRoles()
        for(let p=0;p<this.players;p++){
          let newPlayer = this.createPlayer(p)
          // this.log.push('Player ' + p + ': ' + newPlayer.role)
          this.persons.push(newPlayer)
        }

        this.end = false

        while(this.end === false){
          this.killBy('werewolf')
          if(this.checkGameEnd() == true){
            this.wins.werewolves++
            return
          }
          this.killBy('all')
          if(this.checkGameEnd() == true){
            this.wins.villagers++
            return
          }
        }
      }
    }
  }
</script>
