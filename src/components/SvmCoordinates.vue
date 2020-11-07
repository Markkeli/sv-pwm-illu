<template>
    <div class="SvmCoordinates">
        <p id="title">Switch vectors in stationary &alpha;&beta;-coordinates</p>
        <div class="svmCanvas">
            <canvas :width="size*2" :height="size*2" id="svmgraph" @mousemove="handleMouseMove" @click="handleMouseClick"></canvas>
        </div>

        <div class="svmTexts">
            <p>Try moving the cursor along the coordinate system.  The switch
            vectors are illustrated with redcolor. Click on a point on the
            system to trigger a switch <br/>sequence.</p>
            <table>
                <tr>
                    <th>Data</th>
                    <th>Value</th>
                </tr>
                <tr>
                    <td>V<sub>a</sub></td>
                    <td>{{ (v_a/174).toFixed(3) }}</td>
                </tr>
                <tr>
                    <td>V<sub>b</sub></td>
                    <td>{{ (v_b/174).toFixed(3) }}</td>
                </tr>
                <tr>
                    <td>V<sub>c</sub></td>
                    <td>{{ (v_c/174).toFixed(3) }}</td>
                </tr>
                <tr>
                    <td>V<sub>alpha</sub></td>
                    <td>{{ (v_alpha/174).toFixed(3) }}</td>
                </tr>
                <tr>
                    <td>V<sub>beta</sub></td>
                    <td>{{ (v_beta/174).toFixed(3) }}</td>
                </tr>
                <tr>
                    <td>Switch sector</td>
                    <td>{{ svm_sector }}</td>
                </tr>
                <tr>
                    <td>Reference vector length</td>
                    <td>{{ (ref_length/174).toFixed(3) }}</td>
                </tr>
                <tr>
                    <td>Theta</td>
                    <td>{{ theta.toFixed(2) }}</td>
                </tr>
            </table>
        </div>
    </div>

</template>

<script>
var mouseClickTimeout = false;

export default {
    name: 'SvmCoordinates',
    props: {
        freq: Number,
        opmode: Boolean,
        autostep: Number,
        refAmplitude: Number
    },
    data: function() { 
        return {
            mouse: {
                x: 0,
                y: 0
            },
            v_alpha: 0,
            v_beta: 0,
            v_a: 0,
            v_b: 0,
            v_c: 0,
            svm_sector: 0,
            ref_length: 0,
            theta: 0,
            size: 200,
            automodulator: null,
            runningTheta: 0
        };
    },

    mounted() { 
        this.c = document.getElementById("svmgraph");
        this.ctx = this.c.getContext("2d");
        
        this.drawBackground();

    },
    watch: {
        opmode: {
            handler: function() {
                this.autoModulation();
            },
            deep: true
        }
    },

    methods: {
      
        drawBackground: function () {
            var side = 0;
            var x = this.size;
            var y = this.size;

            // Drawing the hexagon
            this.ctx.beginPath();
            this.ctx.moveTo(x + this.size * Math.cos(0), y + this.size * Math.sin(0));
            for (side; side < 7; side++) {
                this.ctx.lineTo(x + this.size * Math.cos(side * 2 * Math.PI / 6), y + this.size * Math.sin(side * 2 * Math.PI / 6));
            }
            this.ctx.stroke()

            // Drawing the circle inside the hexagon
            this.ctx.beginPath();
            this.ctx.arc(x, y, Math.sqrt(3)/2*this.size, 0, 2 * Math.PI);
            this.ctx.stroke()

            // Drawing modulation segments
            side = 0;
            for (side; side < 6; side++) {
                this.ctx.beginPath();
                this.ctx.moveTo(x, y);
                this.ctx.lineTo(x + this.size * Math.cos(side * 2 * Math.PI / 6), y + this.size * Math.sin(side * 2 * Math.PI / 6));
                this.ctx.stroke()
            }
          
        },

        drawVectors: function (mouse_x, mouse_y) {
            this.ctx.lineWidth = 1;
            this.ctx.strokeStyle = "#000000";
            this.ctx.clearRect(0, 0, 600, 600);
            this.drawBackground();

            // Draw reference vector
            this.ctx.beginPath();
            this.ctx.moveTo(this.size, this.size);
            this.ctx.lineTo(mouse_x, mouse_y);
            this.ctx.stroke()

            // Draw switch vectors
            
            var x = this.size;
            var y = this.size;

            var side = 6 - this.svm_sector;
            var sidetwo = 0;

            if (side < 6) sidetwo = side+1;
            else sidetwo = 6;
            
            var right_switch_vector_length = 0;
            var left_switch_vector_length = 0;
            var two_by_sqrt3 = 2/Math.sqrt(3);


            switch (this.svm_sector) {
                case 1:
                    right_switch_vector_length = two_by_sqrt3 * this.ref_length * Math.cos(this.theta + Math.PI/6);
                    left_switch_vector_length = two_by_sqrt3 * this.ref_length * Math.sin(this.theta);
                    break;
                case 2:
                    right_switch_vector_length = two_by_sqrt3 * this.ref_length * Math.sin(this.theta + Math.PI/3); 
                    left_switch_vector_length = - two_by_sqrt3 * this.ref_length * Math.cos(this.theta + Math.PI/6);
                    break;                          
                case 3:
                    right_switch_vector_length = two_by_sqrt3 * this.ref_length * Math.sin(this.theta);
                    left_switch_vector_length = - two_by_sqrt3 * this.ref_length * Math.sin(this.theta + Math.PI/3); 
                    break;
                case 4:
                    right_switch_vector_length = - two_by_sqrt3 * this.ref_length * Math.cos(this.theta + Math.PI/6);
                    left_switch_vector_length = - two_by_sqrt3 * this.ref_length * Math.sin(this.theta);
                    break;     
                case 5:
                    right_switch_vector_length = - two_by_sqrt3 * this.ref_length * Math.sin(this.theta + Math.PI/3); 
                    left_switch_vector_length = two_by_sqrt3 * this.ref_length * Math.cos(this.theta + Math.PI/6);
                    break;     
                case 6:
                    right_switch_vector_length = - two_by_sqrt3 * this.ref_length * Math.sin(this.theta);
                    left_switch_vector_length = two_by_sqrt3 * this.ref_length * Math.sin(this.theta + Math.PI/3);
                    break;     
            }

            this.ctx.lineWidth = 3;
            this.ctx.strokeStyle = "#FF0000";

            this.ctx.beginPath();
            this.ctx.moveTo(this.size, this.size);
            this.ctx.lineTo(x + left_switch_vector_length * Math.cos(side * 2 * Math.PI / 6), y + left_switch_vector_length * Math.sin(side * 2 * Math.PI / 6));
            this.ctx.stroke()

            this.ctx.beginPath();
            this.ctx.moveTo(this.size, this.size);
            this.ctx.lineTo(x + right_switch_vector_length * Math.cos(sidetwo * 2 * Math.PI / 6), y + right_switch_vector_length * Math.sin(sidetwo * 2 * Math.PI / 6));
            this.ctx.stroke()
                    

            
        },

        updateSpaceVectorData: function (v_alpha, v_beta) {

            // Sector determination
            var sector = this.determineSector(v_alpha, v_beta);
            
            this.v_alpha = v_alpha;
            this.v_beta = v_beta;

            this.v_a = v_alpha;
            this.v_b = -0.5*v_alpha + Math.sqrt(3)/2*v_beta;
            this.v_c = -0.5*v_alpha - Math.sqrt(3)/2*v_beta;
            this.$emit('momentaryVoltages', [this.v_a, this.v_b, this.v_c]);

            this.svm_sector = sector;
            this.ref_length = Math.sqrt(v_alpha*v_alpha + v_beta*v_beta);
            var theta_temp = Math.atan2(this.v_beta, this.v_alpha);
            if ( theta_temp < 0 ) this.theta = 2*Math.PI + theta_temp;
            else this.theta = theta_temp;
        },

        handleMouseMove: function (event) {
            if (! this.opmode ) {
                
                if (! mouseClickTimeout ) {
                    var rect = this.c.getBoundingClientRect();
                    this.mouse = {
                        x: event.pageX - rect.left,
                        y: event.pageY - rect.top
                    }

                    this.updateSpaceVectorData(this.mouse.x - this.size, this.size - this.mouse.y);
                    this.drawVectors(this.mouse.x, this.mouse.y);
                }
            }

        },
      
        autoModulation: function () {
            if ( this.opmode ) {

                this.automodulator = setInterval(() =>  {
                    var v_alpha_generated = this.refAmplitude*Math.cos(this.runningTheta);
                    var v_beta_generated = this.refAmplitude*Math.sin(this.runningTheta);

                    this.updateSpaceVectorData(v_alpha_generated, v_beta_generated);
                    this.drawVectors(v_alpha_generated+this.size, -v_beta_generated+this.size);
                    this.$emit('triggerModulation', [this.v_alpha/this.size, this.v_beta/this.size]); // normalization
                    

                    this.runningTheta += 0.1*Math.PI;

                    if (this.runningTheta > 2*Math.Pi ) {
                        this.runningTheta = 0;
                    }

                    
                },  this.autostep)
            }
            else {
                clearInterval(this.automodulator);
            }
        },

        handleMouseClick: function (event) {
            if ((! mouseClickTimeout) && (! this.opmode) ) {
                mouseClickTimeout = true;
                setTimeout(function() {
                    mouseClickTimeout = false;
                }, 1/this.freq*1000);

                var rect = this.c.getBoundingClientRect();
                this.mouse = {
                    x: event.pageX - rect.left,
                    y: event.pageY - rect.top
                }

                this.updateSpaceVectorData(this.mouse.x - this.size, this.size - this.mouse.y);
                this.drawVectors(this.mouse.x, this.mouse.y);
                this.$emit('triggerModulation', [this.v_alpha/this.size, this.v_beta/this.size]); // normalization
            }
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

<style scoped>

#title {
    text-align: center;
    position: relative;
    font-size:120%;
    font-weight: 700;
}
.svmCanvas {
    display: inline-block;
    position: relative;
    margin-left: 30px;
    top: -20px;

}
.svmTexts {
    display: inline-block;
    position: relative;
    top: -275px;
    margin-left: 20px;
    font-family: 'Segoe UI';
    text-align: justify;
    width: 220px;
    font-size: 90%;
        padding: 5px;

}

</style>
