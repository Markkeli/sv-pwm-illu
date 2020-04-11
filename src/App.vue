<template>
        <div id="app">
            <SvmCoordinates v-on:triggerModulation="modulate" v-on:momentaryVoltages="updateVoltages" v-bind:freq="switchingFrequency" />
            <TwoLevelBridge v-bind:switchPositions="positions" v-bind:freq="switchingFrequency" />
            <Waveform v-bind:newSignal="voltageSignal" v-bind:voltages="momentaryVoltages" id="wf" />
            <div id="inputGUI">
                <p id="frequencyText">Switching frequency</p>
                <input v-model="switchingFrequency" type="range" min="0.1" max="5" value="1" class="slider" id="frequencyRange">
            </div>

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
            momentaryVoltages:[0, 0, 0],
            voltageSignal: [[], [], []],
            currentSignal: [[], [], []],
            switchingFrequency: 1,  // in Hz
            Udc: 1
        }
  },
  mounted() {

  },
  methods: {
    
    updateVoltages(voltages) {
        this.momentaryVoltages = voltages;
    },

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
        var fullPeriod = 1/this.switchingFrequency*1000; // ms
        var halfPeriod = 0.5 * fullPeriod;
        
        // voltageSignal data init
        for (var i = 0; i < 100; ++i) {
            this.voltageSignal[i] = 0;
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
        var voltageSequence_an = [];
        var voltageSequence_bn = [];
        var voltageSequence_cn = [];        


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
                    voltageSequence_an[i] = 0;
                    voltageSequence_bn[i] = 0;
                    voltageSequence_cn[i] = 0;

                    break;
                }
                case v1: {
                    voltageSequence_ab[i] = this.Udc;
                    voltageSequence_bc[i] = 0;
                    voltageSequence_ca[i] = -this.Udc;
                    voltageSequence_an[i] = 2/3*this.Udc;
                    voltageSequence_bn[i] = -1/3*this.Udc;
                    voltageSequence_cn[i] = -1/3*this.Udc;
                    break;
                }
                case v2: {
                    voltageSequence_ab[i] = 0;
                    voltageSequence_bc[i] = this.Udc;
                    voltageSequence_ca[i] = -this.Udc;
                    voltageSequence_an[i] = 1/3*this.Udc;
                    voltageSequence_bn[i] = 1/3*this.Udc;
                    voltageSequence_cn[i] = -2/3*this.Udc;                                        
                    break;
                }
                case v3: {
                    voltageSequence_ab[i] = -this.Udc;
                    voltageSequence_bc[i] = this.Udc;
                    voltageSequence_ca[i] = 0;            
                    voltageSequence_an[i] = -1/3*this.Udc;
                    voltageSequence_bn[i] = 2/3*this.Udc;
                    voltageSequence_cn[i] = -1/3*this.Udc;                                       
                    break;
                }
                case v4: {
                    voltageSequence_ab[i] = -this.Udc;
                    voltageSequence_bc[i] = 0;
                    voltageSequence_ca[i] = this.Udc;
                    voltageSequence_an[i] = -2/3*this.Udc;
                    voltageSequence_bn[i] = 1/3*this.Udc;
                    voltageSequence_cn[i] = 1/3*this.Udc;                          
                    break;
                }            
                case v5: {
                    voltageSequence_ab[i] = 0;
                    voltageSequence_bc[i] = -this.Udc;
                    voltageSequence_ca[i] = this.Udc;      
                    voltageSequence_an[i] = -1/3*this.Udc;
                    voltageSequence_bn[i] = -1/3*this.Udc;
                    voltageSequence_cn[i] = 2/3*this.Udc;                                        
                    break;
                }
                case v6: {
                    voltageSequence_ab[i] = this.Udc;
                    voltageSequence_bc[i] = -this.Udc;
                    voltageSequence_ca[i] = 0;          
                    voltageSequence_an[i] = 1/3*this.Udc;
                    voltageSequence_bn[i] = -2/3*this.Udc;
                    voltageSequence_cn[i] = 1/3*this.Udc;                                             
                    break;
                }
                case v7: {
                    voltageSequence_ab[i] = 0;
                    voltageSequence_bc[i] = 0;
                    voltageSequence_ca[i] = 0;         
                    voltageSequence_an[i] = 0;
                    voltageSequence_bn[i] = 0;
                    voltageSequence_cn[i] = 0;                               
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
        var voltageGraph_an = [];
        var voltageGraph_bn = [];
        var voltageGraph_cn = [];                   

        for ( i_graph = 0; i_graph < firstStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[0].toFixed(1);
            voltageGraph_bc[i_graph] = voltageSequence_bc[0].toFixed(1);
            voltageGraph_ca[i_graph] = voltageSequence_ca[0].toFixed(1);
            voltageGraph_an[i_graph] = voltageSequence_an[0].toFixed(1);
            voltageGraph_bn[i_graph] = voltageSequence_bn[0].toFixed(1);
            voltageGraph_cn[i_graph] = voltageSequence_cn[0].toFixed(1);            
        }
        for ( i_graph = firstStep; i_graph < secondStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[1].toFixed(1);
            voltageGraph_bc[i_graph] = voltageSequence_bc[1].toFixed(1);
            voltageGraph_ca[i_graph] = voltageSequence_ca[1].toFixed(1);       
            voltageGraph_an[i_graph] = voltageSequence_an[1].toFixed(1);
            voltageGraph_bn[i_graph] = voltageSequence_bn[1].toFixed(1);
            voltageGraph_cn[i_graph] = voltageSequence_cn[1].toFixed(1);                   
        }    
        for ( i_graph = secondStep; i_graph < thirdStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[2].toFixed(1);
            voltageGraph_bc[i_graph] = voltageSequence_bc[2].toFixed(1);
            voltageGraph_ca[i_graph] = voltageSequence_ca[2].toFixed(1);  
            voltageGraph_an[i_graph] = voltageSequence_an[2].toFixed(1);
            voltageGraph_bn[i_graph] = voltageSequence_bn[2].toFixed(1);
            voltageGraph_cn[i_graph] = voltageSequence_cn[2].toFixed(1);                        
        }            
        for ( i_graph = thirdStep; i_graph < fourthStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[3].toFixed(1);
            voltageGraph_bc[i_graph] = voltageSequence_bc[3].toFixed(1);
            voltageGraph_ca[i_graph] = voltageSequence_ca[3].toFixed(1);
            voltageGraph_an[i_graph] = voltageSequence_an[3].toFixed(1);
            voltageGraph_bn[i_graph] = voltageSequence_bn[3].toFixed(1);
            voltageGraph_cn[i_graph] = voltageSequence_cn[3].toFixed(1);                          
        }        
        for ( i_graph = fourthStep; i_graph < fifthStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[4].toFixed(1);
            voltageGraph_bc[i_graph] = voltageSequence_bc[4].toFixed(1);
            voltageGraph_ca[i_graph] = voltageSequence_ca[4].toFixed(1);
            voltageGraph_an[i_graph] = voltageSequence_an[4].toFixed(1);
            voltageGraph_bn[i_graph] = voltageSequence_bn[4].toFixed(1);
            voltageGraph_cn[i_graph] = voltageSequence_cn[4].toFixed(1);                          
        }
        for ( i_graph = fifthStep; i_graph < sixthStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[5].toFixed(1);
            voltageGraph_bc[i_graph] = voltageSequence_bc[5].toFixed(1);
            voltageGraph_ca[i_graph] = voltageSequence_ca[5].toFixed(1); 
            voltageGraph_an[i_graph] = voltageSequence_an[5].toFixed(1);
            voltageGraph_bn[i_graph] = voltageSequence_bn[5].toFixed(1);
            voltageGraph_cn[i_graph] = voltageSequence_cn[5].toFixed(1);                         
        }
        for ( i_graph = sixthStep; i_graph < seventhStep; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[6].toFixed(1);
            voltageGraph_bc[i_graph] = voltageSequence_bc[6].toFixed(1);
            voltageGraph_ca[i_graph] = voltageSequence_ca[6].toFixed(1); 
            voltageGraph_an[i_graph] = voltageSequence_an[6].toFixed(1);
            voltageGraph_bn[i_graph] = voltageSequence_bn[6].toFixed(1);
            voltageGraph_cn[i_graph] = voltageSequence_cn[6].toFixed(1);                         
        }  
        for ( i_graph = seventhStep; i_graph < stepsInGraph; ++i_graph ) {
            voltageGraph_ab[i_graph] = voltageSequence_ab[7].toFixed(1);
            voltageGraph_bc[i_graph] = voltageSequence_bc[7].toFixed(1);
            voltageGraph_ca[i_graph] = voltageSequence_ca[7].toFixed(1);   
            voltageGraph_an[i_graph] = voltageSequence_an[7].toFixed(1);
            voltageGraph_bn[i_graph] = voltageSequence_bn[7].toFixed(1);
            voltageGraph_cn[i_graph] = voltageSequence_cn[7].toFixed(1);                        
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


        this.voltageSignal = [ voltageGraph_ab, voltageGraph_bc, voltageGraph_ca, voltageGraph_an, voltageGraph_bn, voltageGraph_cn ];

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
    display:inline-block;
    margin-left: 20px;
}
.TwoLevelBridge {
    float:left;
}

.slider {
  -webkit-appearance: none;
  width: 200px;
  height: 15px;
  border-radius: 5px;  
  background: #4AFF5f;
  outline: none;
  opacity: 0.7;
  -webkit-transition: .2s;
  transition: opacity .2s;
}

.slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 25px;
  height: 25px;
  border-radius: 50%; 
  background: #4CAF50;
  cursor: pointer;
}

.slider::-moz-range-thumb {
  width: 25px;
  height: 25px;
  border-radius: 50%;
  background: #4CAF50;
  cursor: pointer;
}

#inputGUI {
    position: absolute;
    top: 500px;
    right: 400px;
}


</style>
