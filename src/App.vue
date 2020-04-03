<template>
        <div id="app">
           <SvmCoordinates v-on:triggerModulation="modulate" />
           <TwoLevelBridge v-bind:switchPositions="positions" />

        </div>
</template>

<script>

import SvmCoordinates from './components/SvmCoordinates.vue'
import TwoLevelBridge from './components/TwoLevelBridge.vue'

export default {
  name: 'App',
  components: {
    SvmCoordinates,
    TwoLevelBridge
  },
  data() { 
    return {
      positions:[0,0,0,0,0,0]
    }
  },
  methods: {
    
    modulate(voltageReference) {
        // Switch vectors
        // Order: S1, S2, S3, S4, S5, S6
        // where switches 1-2 are part of A-phase leg etc.
        var v0 = [1,0,1,0,1,0];
        var v1 = [0,1,1,0,1,0];
        var v2 = [0,1,0,1,1,0];
        var v3 = [1,0,0,1,1,0];
        var v4 = [1,0,0,1,0,1];
        var v5 = [1,0,1,0,0,1];
        var v6 = [0,1,1,0,0,1];
        var v7 = [0,1,0,1,0,1];


        var v_alpha = voltageReference[0];
        var v_beta = voltageReference[1];
        var sector = this.determineSector(v_alpha, v_beta);
        var t1, t2, t0 = 0;
        var t_first = 0;
        var t_second = 0;
        var t_third = 0;
        var t_fourth = 0;
        var t_fifth = 0;
        var t_sixth = 0;
        var t_seventh = 0;        
        var one_by_sqrt3 = 1/Math.sqrt(3);
        var two_by_sqrt3 = 2/Math.sqrt(3);
        var fullPeriod = 500; // ms
        var halfPeriod = 0.5 * fullPeriod;

        // Switch timings
        switch (sector) {

            // sector 1
            case 1: {
                // Switch vector on times
                t1 = (v_alpha - one_by_sqrt3 * v_beta) * halfPeriod;
                t2 = (two_by_sqrt3 * v_beta) * halfPeriod;
                t0 = halfPeriod - t1 - t2;

                // PWM timings in a half period
                t_first = t0 / 2;
                t_second = t_first + t1;
                t_third = t_second + t2;
                t_fourth = t_third + t0 / 2;
                t_fifth = t_fourth + t0 / 2;
                t_sixth = t_fifth + t2;
                t_seventh = t_sixth + t1;

                break;
            }

                // sector 2
            case 2: {
                // Switch vector on times
                t1 = (v_alpha + one_by_sqrt3 * v_beta) * halfPeriod;
                t2 = (-v_alpha + one_by_sqrt3 * v_beta) * halfPeriod;
                t0 = halfPeriod - t1 - t2;

                // PWM timings in a half period
                t_first = t0 / 2;
                t_second = t_first + t2;
                t_third = t_second + t1;
                t_fourth = t_third + t0 / 2;
                t_fifth = t_fourth + t0 / 2;
                t_sixth = t_fifth + t1;
                t_seventh = t_sixth + t2;

                break;
            }

                // sector 3
            case 3: {
                // Switch vector on times
                t1 = (two_by_sqrt3 * v_beta) * halfPeriod;
                t2 = (-v_alpha - one_by_sqrt3 * v_beta) * halfPeriod;
                t0 = halfPeriod - t1 - t2;

                // PWM timings in a half period
                t_first = t0 / 2;
                t_second = t_first + t1;
                t_third = t_second + t2;
                t_fourth = t_third + t0 / 2;
                t_fifth = t_fourth + t0 / 2;
                t_sixth = t_fifth + t2;
                t_seventh = t_sixth + t1;

                break;
            }

                // sector 4
            case 4: {
                // Switch vector on times
                t1 = (-v_alpha + one_by_sqrt3 * v_beta) * halfPeriod;
                t2 = (-two_by_sqrt3 * v_beta) * halfPeriod;
                t0 = halfPeriod - t1 - t2;

                // PWM timings in a half period
                t_first = t0 / 2;
                t_second = t_first + t2;
                t_third = t_second + t1;
                t_fourth = t_third + t0 / 2;
                t_fifth = t_fourth + t0 / 2;
                t_sixth = t_fifth + t1;
                t_seventh = t_sixth + t2;
            

                break;
            }

                // sector 5
            case 5: {
                // Switch vector on times
                t1 = (-v_alpha - one_by_sqrt3 * v_beta) * halfPeriod;
                t2 = (v_alpha - one_by_sqrt3 * v_beta) * halfPeriod;
                t0 = halfPeriod - t1 - t2;

                // PWM timings in a half period
                t_first = t0 / 2;
                t_second = t_first + t1;
                t_third = t_second + t2;
                t_fourth = t_third + t0 / 2;
                t_fifth = t_fourth + t0 / 2;
                t_sixth = t_fifth + t2;
                t_seventh = t_sixth + t1;           

                break;
            }

                // sector 6
            case 6: {
                // Switch vector on times
                t1 = (-two_by_sqrt3 * v_beta) * halfPeriod;
                t2 = (v_alpha + one_by_sqrt3 * v_beta) * halfPeriod;
                t0 = halfPeriod - t1 - t2;

                // PWM timings in a half period
                t_first = t0 / 2;
                t_second = t_first + t2;
                t_third = t_second + t1;
                t_fourth = t_third + t0 / 2;
                t_fifth = t_fourth + t0 / 2;
                t_sixth = t_fifth + t1;
                t_seventh = t_sixth + t2;             


                break;
            }
        }

        // Limitation

        if (t_first < 0) {
            t_first = 0;
        } 
        else if (t_second > halfPeriod) {
            t_second = halfPeriod;
        }

        if (t_second < 0) {
            t_second = 0;
        } 
        else if (t_second > halfPeriod) {
            t_second = halfPeriod;
        }

        if (t_third < 0) {
            t_third = 0;
        } 
        else if (t_third > halfPeriod) {
            t_third = halfPeriod;
        }

        if (t_fourth < halfPeriod) {
            t_fourth= halfPeriod;
        } 
        else if (t_fourth > fullPeriod) {
            t_fourth = fullPeriod;
        }

        if (t_fifth < halfPeriod) {
            t_fifth = halfPeriod;
        } 
        else if (t_fourth > fullPeriod) {
            t_fifth = fullPeriod;
        }    
        
        if (t_sixth < halfPeriod) {
            t_sixth = halfPeriod;
        } 
        else if (t_sixth > fullPeriod) {
            t_sixth = fullPeriod;
        }        

        if (t_seventh < halfPeriod) {
            t_seventh = halfPeriod;
        } 
        else if (t_seventh > fullPeriod) {
            t_seventh = fullPeriod;
        }        
                
    
        // Switch sequence

        switch (sector) {
            case 1: {

                this.positions = v0;

                setTimeout(() => {
                    this.positions = v1;
                }, t_first);

                setTimeout(() => {
                    this.positions = v2;
                }, t_second);

                setTimeout(() => {
                    this.positions = v7;
                }, t_third);

                setTimeout(() => {
                    this.positions = v7;
                }, t_fourth);      

                setTimeout(() => {
                    this.positions = v2;
                }, t_fifth);         

                setTimeout(() => {
                    this.positions = v1;
                }, t_sixth);  

                setTimeout(() => {
                    this.positions = v0;
                }, t_seventh);  

                break;
            }
            case 2: {

                this.positions = v0;

                setTimeout(() => {
                    this.positions = v3;
                }, t_first);

                setTimeout(() => {
                    this.positions = v2;
                }, t_second);

                setTimeout(() => {
                    this.positions = v7;
                }, t_third);

                setTimeout(() => {
                    this.positions = v7;
                }, t_fourth);      

                setTimeout(() => {
                    this.positions = v2;
                }, t_fifth);         

                setTimeout(() => {
                    this.positions = v3;
                }, t_sixth);  

                setTimeout(() => {
                    this.positions = v0;
                }, t_seventh);  
                
                break;
            }
            case 3: {

                this.positions = v0;

                setTimeout(() => {
                    this.positions = v3;
                }, t_first);

                setTimeout(() => {
                    this.positions = v4;
                }, t_second);

                setTimeout(() => {
                    this.positions = v7;
                }, t_third);

                setTimeout(() => {
                    this.positions = v7;
                }, t_fourth);      

                setTimeout(() => {
                    this.positions = v4;
                }, t_fifth);         

                setTimeout(() => {
                    this.positions = v3;
                }, t_sixth);  

                setTimeout(() => {
                    this.positions = v0;
                }, t_seventh);  
                
                break;
            }
            case 4: {

                setTimeout(() => {
                    this.positions = v4;
                }, t_first);

                setTimeout(() => {
                    this.positions = v4;
                }, t_second);

                setTimeout(() => {
                    this.positions = v7;
                }, t_third);

                setTimeout(() => {
                    this.positions = v7;
                }, t_fourth);      

                setTimeout(() => {
                    this.positions = v4;
                }, t_fifth);         

                setTimeout(() => {
                    this.positions = v5;
                }, t_sixth);  

                setTimeout(() => {
                    this.positions = v0;
                }, t_seventh);                  
                
                break;
            }
            case 5: {


                setTimeout(() => {
                    this.positions = v5;
                }, t_first);

                setTimeout(() => {
                    this.positions = v6;
                }, t_second);

                setTimeout(() => {
                    this.positions = v7;
                }, t_third);

                setTimeout(() => {
                    this.positions = v7;
                }, t_fourth);      

                setTimeout(() => {
                    this.positions = v6;
                }, t_fifth);         

                setTimeout(() => {
                    this.positions = v5;
                }, t_sixth);  

                setTimeout(() => {
                    this.positions = v0;
                }, t_seventh);                  
                
                break;
            }
            case 6: {

                setTimeout(() => {
                    this.positions = v1;
                }, t_first);

                setTimeout(() => {
                    this.positions = v6;
                }, t_second);

                setTimeout(() => {
                    this.positions = v7;
                }, t_third);

                setTimeout(() => {
                    this.positions = v7;
                }, t_fourth);      

                setTimeout(() => {
                    this.positions = v6;
                }, t_fifth);         

                setTimeout(() => {
                    this.positions = v1;
                }, t_sixth);  

                setTimeout(() => {
                    this.positions = v0;
                }, t_seventh);                  
                
                break;
            }                                                            

        }


        console.log("switch sequence complete!")

    },
      
    determineSector: function (v_alpha, v_beta) {

      var one_sqrt3_v_beta = 1/Math.sqrt(3) * v_beta;
      var sector = 1;

      if (v_beta >= 0) {
          if (v_alpha >= 0) {
              // quadrant I
              if (one_sqrt3_v_beta > v_alpha) {
                  sector = 2;
              } else {
                  sector = 1;
              }
          } else {
              // quadrant II
              if (-one_sqrt3_v_beta > v_alpha) {
                  sector = 3;
              } else {
                  sector = 2;
              }
          }
      } else {
          if (v_alpha >= 0.) {
              // quadrant IV
              if (-one_sqrt3_v_beta > v_alpha) {
                  sector = 5;
              } else {
                  sector = 6;
              }
          } 
          else {
              // quadrant III
              if (one_sqrt3_v_beta > v_alpha) {
                  sector = 4;
              } else {
                  sector = 5;
              }
          }
      }

      return sector;
    }
  }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 60px;
  
}

.SvmCoordinates {
    float: left;
    width: 100px;
    height: 20px;
    margin-right: 8px;
}
.TwoLevelBridge {
    margin-left: 800px;
}

</style>
