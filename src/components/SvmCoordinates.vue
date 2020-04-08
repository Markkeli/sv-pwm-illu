<template>
  <div class="SvmCoordinates">
      <canvas width="600" height="600" id="svmgraph" @mousemove="handleMouseMove" @click="handleMouseClick"></canvas>
      <p id>V_alpha: {{ v_alpha }}</p>
      <p id>V_beta: {{ v_beta }}</p>
      <p id>sector: {{ svm_sector }}</p>
      <p id>reference vector length: {{ ref_length }}</p>
      <p id>theta: {{ theta }}</p>
  </div>
</template>

<script>
var mouseClickTimeout = false;

export default {
  name: 'SvmCoordinates',
  data: function() { 
    return {
      mouse: {
          x: 0,
          y: 0
      },
      v_alpha: 0,
      v_beta: 0,
      svm_sector: 0,
      ref_length: 0,
      theta: 0
    };
  },

  mounted() { 
      this.c = document.getElementById("svmgraph");
      this.ctx = this.c.getContext("2d");
      this.size = 300;
      
      this.drawBackground();

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
          this.svm_sector = sector;
          this.ref_length = Math.sqrt(v_alpha*v_alpha + v_beta*v_beta);
          var theta_temp = Math.atan2(this.v_beta, this.v_alpha);
          if ( theta_temp < 0 ) this.theta = 2*Math.PI + theta_temp;
          else this.theta = theta_temp;

      },
      handleMouseMove: function (event) {
          //console.log(this.mouseClickTimeout);

          if (! mouseClickTimeout ) {
            var rect = this.c.getBoundingClientRect();
            this.mouse = {
                x: event.pageX - rect.left,
                y: event.pageY - rect.top
            }

            this.updateSpaceVectorData(this.mouse.x - this.size, this.size - this.mouse.y);
            this.drawVectors(this.mouse.x, this.mouse.y);
          }
      },

    handleMouseClick: function (event) {
        if (! mouseClickTimeout ) {
            mouseClickTimeout = true;
            setTimeout(function() {
                mouseClickTimeout = false;
            }, 2000);

            var rect = this.c.getBoundingClientRect();
            this.mouse = {
                x: event.pageX - rect.left,
                y: event.pageY - rect.top
            }

            this.updateSpaceVectorData(this.mouse.x - this.size, this.size - this.mouse.y);
            this.drawVectors(this.mouse.x, this.mouse.y);
            // normalization
            this.$emit('triggerModulation', [this.v_alpha/this.size, this.v_beta/this.size]);
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

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

</style>
