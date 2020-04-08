<template>
        <div id="app">
            <SvmCoordinates v-on:triggerModulation="modulate" />
            <TwoLevelBridge v-bind:switchPositions="positions" />
            <Waveform v-bind:newSignal="signal" id="wf" />

        </div>
</template>

<script>

import SvmCoordinates from './components/SvmCoordinates.vue'
import TwoLevelBridge from './components/TwoLevelBridge.vue'
import Waveform from './components/Waveform.vue'

export default {
  name: 'App',
  components: {
    SvmCoordinates,
    TwoLevelBridge,
    Waveform
  },
  data() { 
    return {
      positions: [0,0,0,0,0,0],
      signal: [[], [], []]
    }
  },
  mounted() {
    for (var i = 0; i < 100; ++i) {
            this.signal[0][i] = 0;
            this.signal[1][i] = 0;
            this.signal[2][i] = 0;        
    }
  },
  methods: {
    
    modulate(voltageReference) {
        // Switch vectors
        // Order: S1, S2, S3, S4, S5, S6
        // where switches 1-2 are part of A-phase leg etc.
        var Udc = 80;

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
        var fullPeriod = 2000; // ms
        var halfPeriod = 0.5 * fullPeriod;
        
        // Signal data init
        for (var i = 0; i < 100; ++i) {
            this.signal[i] = 0;
        }

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
        
        // Define the switch and voltage sequences for each sector
        var switchSequence = [];
        var voltageSequence_ab = [];
        var voltageSequence_bc = [];
        var voltageSequence_ca = [];


        switch (sector) {
            case 1: {
                switchSequence = [v0, v1, v2, v7, v7, v2, v1, v0];
                break;
            }
            case 2: {
                switchSequence = [v0, v3, v2, v7, v7, v2, v3, v0];
                break;
            }
            case 3: {
                switchSequence = [v0, v3, v4, v7, v7, v4, v3, v0];
                break;
            }
            case 4: {
                switchSequence = [v0, v5, v4, v7, v7, v4, v5, v0];
                break;
            }
            case 5: {
                switchSequence = [v0, v5, v6, v7, v7, v6, v5, v0];
                break;
            }            
            case 6: {
                switchSequence = [v0, v1, v6, v7, v7, v6, v1, v0];
                break;
            }
        }

        for ( i = 0; i < 8; ++i ) {
            switch (switchSequence[i]) {
                case v0: {
                    voltageSequence_ab[i] = 0;
                    voltageSequence_bc[i] = 0;
                    voltageSequence_ca[i] = 0;
                    break;
                }
                case v1: {
                    voltageSequence_ab[i] = Udc;
                    voltageSequence_bc[i] = 0;
                    voltageSequence_ca[i] = -Udc;
                    break;
                }
                case v2: {
                    voltageSequence_ab[i] = 0;
                    voltageSequence_bc[i] = Udc;
                    voltageSequence_ca[i] = -Udc;                    
                    break;
                }
                case v3: {
                    voltageSequence_ab[i] = -Udc;
                    voltageSequence_bc[i] = Udc;
                    voltageSequence_ca[i] = 0;                        
                    break;
                }
                case v4: {
                    voltageSequence_ab[i] = -Udc;
                    voltageSequence_bc[i] = 0;
                    voltageSequence_ca[i] = Udc;    
                    break;
                }            
                case v5: {
                    voltageSequence_ab[i] = 0;
                    voltageSequence_bc[i] = -Udc;
                    voltageSequence_ca[i] = Udc;                        
                    break;
                }
                case v6: {
                    voltageSequence_ab[i] = Udc;
                    voltageSequence_bc[i] = -Udc;
                    voltageSequence_ca[i] = 0;                       
                    break;
                }
                case v7: {
                    voltageSequence_ab[i] = 0;
                    voltageSequence_bc[i] = 0;
                    voltageSequence_ca[i] = 0;                    
                    break;
                }                                
            }
            
        }

        // Calculate indexes for the graph values and draw the graph
        var stepsInGraph = 100;
        var firstStep = Math.floor(t_first/fullPeriod*stepsInGraph);
        var secondStep = Math.floor(t_second/fullPeriod*stepsInGraph);
        var thirdStep = Math.floor(t_third/fullPeriod*stepsInGraph);
        var fourthStep = Math.floor(t_fourth/fullPeriod*stepsInGraph);
        var fifthStep = Math.floor(t_fifth/fullPeriod*stepsInGraph);
        var sixthStep = Math.floor(t_sixth/fullPeriod*stepsInGraph);
        var seventhStep = Math.floor(t_seventh/fullPeriod*stepsInGraph);
        var i_graph = 0;
        var voltageGraph_ab = [];
        var voltageGraph_bc = [];
        var voltageGraph_ca = [];

        for ( i_graph = 0; i_graph < firstStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[0]
            voltageGraph_bc[i_graph] = voltageSequence_bc[0]
            voltageGraph_ca[i_graph] = voltageSequence_ca[0]
        }
        for ( i_graph = firstStep; i_graph < secondStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[1]
            voltageGraph_bc[i_graph] = voltageSequence_bc[1]
            voltageGraph_ca[i_graph] = voltageSequence_ca[1]            
        }    
        for ( i_graph = secondStep; i_graph < thirdStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[2]
            voltageGraph_bc[i_graph] = voltageSequence_bc[2]
            voltageGraph_ca[i_graph] = voltageSequence_ca[2]            
        }            
        for ( i_graph = thirdStep; i_graph < fourthStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[3]
            voltageGraph_bc[i_graph] = voltageSequence_bc[3]
            voltageGraph_ca[i_graph] = voltageSequence_ca[3]            
        }        
        for ( i_graph = fourthStep; i_graph < fifthStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[4]
            voltageGraph_bc[i_graph] = voltageSequence_bc[4]
            voltageGraph_ca[i_graph] = voltageSequence_ca[4]            
        }
        for ( i_graph = fifthStep; i_graph < sixthStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[5]
            voltageGraph_bc[i_graph] = voltageSequence_bc[5]
            voltageGraph_ca[i_graph] = voltageSequence_ca[5]            
        }
        for ( i_graph = sixthStep; i_graph < seventhStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[6]
            voltageGraph_bc[i_graph] = voltageSequence_bc[6]
            voltageGraph_ca[i_graph] = voltageSequence_ca[6]            
        }  
        for ( i_graph = seventhStep; i_graph < stepsInGraph; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[7]
            voltageGraph_bc[i_graph] = voltageSequence_bc[7]
            voltageGraph_ca[i_graph] = voltageSequence_ca[7]            
        }                    

        // Switch sequence

        this.positions = switchSequence[0];

        setTimeout(() => {
            this.positions = switchSequence[1];
        }, t_first);

        setTimeout(() => {
            this.positions = switchSequence[2];
        }, t_second);

        setTimeout(() => {
            this.positions = switchSequence[3];

        }, t_third);

        setTimeout(() => {
            this.positions = switchSequence[4];    
        }, t_fourth);      

        setTimeout(() => {
            this.positions = switchSequence[5];
        }, t_fifth);         

        setTimeout(() => {
            this.positions = switchSequence[6];      
        }, t_sixth);  

        setTimeout(() => {
            this.positions = switchSequence[7];                         
        }, t_seventh);  

        this.signal = [ voltageGraph_ab, voltageGraph_bc, voltageGraph_ca ];

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

#wf {
    margin-left: 800px;
    margin-top: 0px;
    max-height: 200px;
    max-width: 450px;
}

</style>
