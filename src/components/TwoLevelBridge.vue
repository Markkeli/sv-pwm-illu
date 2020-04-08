<template>
    <div class="TwoLevelBridge">
        <svg width = "800" height = "400" >

            <!-- Outline -->
            <line x1="40" y1="20" x2="600" y2="20" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="40" y1="380" x2="600" y2="380" style="stroke:rgb(0,0,0); stroke-width:2" />

            <!-- Capacitor -->
            <line x1="40" y1="20" x2="40" y2="190" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="40" y1="210" x2="40" y2="380" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="20" y1="190" x2="60" y2="190" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="20" y1="210" x2="60" y2="210" style="stroke:rgb(0,0,0); stroke-width:2" />

            <!-- A phase leg -->
            <line x1="250" y1="20" x2="250" y2="50" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line id="switch_S1"  x1="250" y1="50" x2="250" y2="120" style="stroke:rgb(255,0,0); stroke-width:2" />
            <line x1="250" y1="120" x2="250" y2="280" style="stroke:rgb(0,0,0); stroke-width:2" />

            <line x1="290" y1="40" x2="290" y2="130" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="250" y1="40" x2="290" y2="40" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="250" y1="130" x2="290" y2="130" style="stroke:rgb(0,0,0); stroke-width:2" />
            <polygon points="280,95 300,95 290,75" style="fill:black;stroke:black;stroke-width:1" />
            <line x1="280" y1="75" x2="300" y2="75" style="stroke:rgb(0,0,0); stroke-width:2" />

            <line id="switch_S2" x1="250" y1="280" x2="250" y2="350" style="stroke:rgb(255,0,0); stroke-width:2" />
            <line x1="250" y1="350" x2="250" y2="380" style="stroke:rgb(0,0,0); stroke-width:2" />

            <line x1="290" y1="270" x2="290" y2="360" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="250" y1="270" x2="290" y2="270" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="250" y1="360" x2="290" y2="360" style="stroke:rgb(0,0,0); stroke-width:2" />
            <polygon points="280,325 300,325 290,305" style="fill:black;stroke:black;stroke-width:1" />
            <line x1="280" y1="305" x2="300" y2="305" style="stroke:rgb(0,0,0); stroke-width:2" />



            <!-- B phase leg -->
            <line x1="425" y1="20" x2="425" y2="50" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line id="switch_S3"  x1="425" y1="50" x2="425" y2="120" style="stroke:rgb(255,0,0); stroke-width:2" />
            <line x1="425" y1="120" x2="425" y2="280" style="stroke:rgb(0,0,0); stroke-width:2" />

            <line x1="465" y1="40" x2="465" y2="130" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="425" y1="40" x2="465" y2="40" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="425" y1="130" x2="465" y2="130" style="stroke:rgb(0,0,0); stroke-width:2" />
            <polygon points="455,95 475,95 465,75" style="fill:black;stroke:black;stroke-width:1" />
            <line x1="455" y1="75" x2="475" y2="75" style="stroke:rgb(0,0,0); stroke-width:2" />

            <line id="switch_S4"  x1="425" y1="280" x2="425" y2="350" style="stroke:rgb(255,0,0); stroke-width:2" />
            <line x1="425" y1="350" x2="425" y2="380" style="stroke:rgb(0,0,0); stroke-width:2" />

            <line x1="465" y1="270" x2="465" y2="360" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="425" y1="270" x2="465" y2="270" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="425" y1="360" x2="465" y2="360" style="stroke:rgb(0,0,0); stroke-width:2" />
            <polygon points="455,325 475,325 465,305" style="fill:black;stroke:black;stroke-width:1" />  
            <line x1="455" y1="305" x2="475" y2="305" style="stroke:rgb(0,0,0); stroke-width:2" />          

            <!-- C phase leg -->
            <line x1="600" y1="20" x2="600" y2="50" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line id="switch_S5" x1="600" y1="50" x2="600" y2="120" style="stroke:rgb(255,0,0); stroke-width:2" />
            <line x1="600" y1="120" x2="600" y2="280" style="stroke:rgb(0,0,0); stroke-width:2" />

            <line x1="640" y1="40" x2="640" y2="130" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="600" y1="40" x2="640" y2="40" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="600" y1="130" x2="640" y2="130" style="stroke:rgb(0,0,0); stroke-width:2" />
            <polygon points="630,95 650,95 640,75" style="fill:black;stroke:black;stroke-width:1" />    
            <line x1="630" y1="75" x2="650" y2="75" style="stroke:rgb(0,0,0); stroke-width:2" />                    

            <line id="switch_S6" x1="600" y1="280" x2="600" y2="350" style="stroke:rgb(255,0,0); stroke-width:2" />
            <line x1="600" y1="350" x2="600" y2="380" style="stroke:rgb(0,0,0); stroke-width:2" />

            <line x1="640" y1="270" x2="640" y2="360" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="600" y1="270" x2="640" y2="270" style="stroke:rgb(0,0,0); stroke-width:2" />
            <line x1="600" y1="360" x2="640" y2="360" style="stroke:rgb(0,0,0); stroke-width:2" />
            <polygon points="630,325 650,325 640,305" style="fill:black;stroke:black;stroke-width:1" />     
            <line x1="630" y1="305" x2="650" y2="305" style="stroke:rgb(0,0,0); stroke-width:2" />                         

            <!-- Phase A network connection -->
            <line x1="250" y1="160" x2="410" y2="160" style="stroke:rgb(0,0,0); stroke-width:2" />
            <path d="M 410 160 Q 425 140 440 160" style="stroke:rgb(0,0,0); stroke-width:2; fill:none" />
            <line x1="440" y1="160" x2="585" y2="160" style="stroke:rgb(0,0,0); stroke-width:2" />
            <path d="M 585 160 Q 600 140 615 160" style="stroke:rgb(0,0,0); stroke-width:2; fill:none" />
            <line x1="615" y1="160" x2="750" y2="160" style="stroke:rgb(0,0,0); stroke-width:2" />

            <!-- Phase B network connection -->
            <line x1="425" y1="200" x2="585" y2="200" style="stroke:rgb(0,0,0); stroke-width:2" />
            <path d="M 585 200 Q 600 180 615 200" style="stroke:rgb(0,0,0); stroke-width:2; fill:none" />
            <line x1="615" y1="200" x2="750" y2="200" style="stroke:rgb(0,0,0); stroke-width:2" />

            <!-- Phase C network connection -->
            <line x1="600" y1="240" x2="750" y2="240" style="stroke:rgb(0,0,0); stroke-width:2" />

            <!-- Texts -->
            <text x="10" y="30" style="font-size:24">+</text>
            <text x="10" y="380" style="font-size:24">-</text>

            <text x="200" y="90" style="font-size:20">S1</text>
            <text x="200" y="320" style="font-size:20">S2</text>

            <text x="375" y="90" style="font-size:20">S3</text>
            <text x="375" y="320" style="font-size:20">S4</text>
            
            <text x="550" y="90" style="font-size:20">S5</text>
            <text x="550" y="320" style="font-size:20">S6</text>

            <text x="760" y="160" style="font-size:20">A</text>
            <text x="760" y="200" style="font-size:20">B</text>
            <text x="760" y="240" style="font-size:20">C</text>

        </svg>
    </div>
</template>

<script>
    export default {
        name: "TwoLevelBridge",
        props: {
            switchPositions: Array
        },
        data: function() { 
            return {
                switches: [0, 0, 0, 0, 0, 0]
            };
        },
        mounted() { 
            this.S1 = document.getElementById("switch_S1");
            this.S2 = document.getElementById("switch_S2");
            this.S3 = document.getElementById("switch_S3");
            this.S4 = document.getElementById("switch_S4");
            this.S5 = document.getElementById("switch_S5");
            this.S6 = document.getElementById("switch_S6");
         },
        watch: {
            switchPositions: {
                handler: function(val) {
                    this.turnSwitches(val);
                },
                deep: true
            }

        },
        methods: {
            turnSwitches: function (positions) {
                // 1 is closed, 0 is open
                if ( positions[0] == 0 && this.switches[0] == 1 ) {
                    this.S1.setAttribute("x1", "230" );
                }
                else if ( positions[0] == 1 && this.switches[0] == 0 ) {
                    this.S1.setAttribute("x1", "250" );
                }

                if ( positions[1] == 0 && this.switches[1] == 1 ) {
                    this.S2.setAttribute("x1", "230" );
                }
                else if ( positions[1] == 1 && this.switches[1] == 0 ) {
                    this.S2.setAttribute("x1", "250" );
                }

                if ( positions[2] == 0 && this.switches[2] == 1 ) {
                    this.S3.setAttribute("x1", "405" );
                }
                else if ( positions[2] == 1 && this.switches[2] == 0 ) {
                    this.S3.setAttribute("x1", "425" );
                }

                if ( positions[3] == 0 && this.switches[3] == 1 ) {
                    this.S4.setAttribute("x1", "405" );
                }
                else if ( positions[3] == 1 && this.switches[3] == 0 ) {
                    this.S4.setAttribute("x1", "425" );
                }

                if ( positions[4] == 0 && this.switches[4] == 1 ) {
                    this.S5.setAttribute("x1", "580" );
                }
                else if ( positions[4] == 1 && this.switches[4] == 0 ) {
                    this.S5.setAttribute("x1", "600" );
                }            

                if ( positions[5] == 0 && this.switches[5] == 1 ) {
                    this.S6.setAttribute("x1", "580" );
                }
                else if ( positions[5] == 1 && this.switches[5] == 0 ) {
                    this.S6.setAttribute("x1", "600" );
                }        

                this.switches = positions;
                
            }
        }

    }
</script>