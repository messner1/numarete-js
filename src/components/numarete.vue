<template>

  <div class="numarete">
      <h2>The Numa-rete: A Biologically-Inspired Shape Recognition System</h2>
      <h3> A simulation by Craig Messner</h3>
      <br>
      <p> The Numa-rete (or Numarete) was a project created in the Heinz Von Foerster-led Biological Computer Lab at the University of Illinois. Former BCL affiliate and University of Illinois professor Paul Weston <a href=https://www.univie.ac.at/constructivism/HvF/festschrift/weston.html>describes</a> the construction of the machine as: "consisting of an iterated network of identical elements, which could instantly report the number of areas of shadow, i.e., "objects," cast on its photocell "retina," utterly independently of their shape or position." The Numarete was an early experiment in biologically-inspired parallel computing, with Von Foerster himself calling it an "artificial retina." It employs connected layers of photosensitive nodes, Weston's "identical elements" in order to instantaneously detect the number of convex shapes placed on its similarly photosensitive surface, an feat no doubt impressive given the speed of more traditional digital computers in the 1960s and 70s. As he makes it clear in his paper "Perception of Form in Biological and Man-Made Systems," Von Foerster himself saw the machine as replicating the sort of cybernetic interplay between system and self that leads to gestalt notions of number (one-ness, two-ness, so forth and so on). The combination of convenient mathematical rules (in this case those governing convex shapes) and simple "biological" elements results in a complex calculation without the need for the computational power or time a more traditional iterative algorithm might demand. In Von Foerster's account, this interplay is what accounts for biology's striking success in areas computers  fear to tread.</p>

      <p> The below is a simulation of the Numarete. Clicking in the field begins and ends lines, allowing for the creation of arbitrary shapes. If the shape is not convex, the simulation will remove it. Adding a shape immediately calculates the number now contained in the field in a fashion (as best I can tell) identical to the original machine. Below the field are boxes indicating the state of each node in each layer, with a brief description of the layer's purpose and function. The counting task itself is redundant in this modern form. After all, the simulation already detects convex shapes in order to make sure you are creating one. However, I hope that this simulation captures at least some of the wonder Numarete no doubt provoked in the era of its creation. It, and many projects of the BCL, are unjustly forgotten. Even Weston himself notes that he's not sure what happened to the original machine! Despite this, projects such as this one or the <a href=https://en.wikipedia.org/wiki/FERMIAC>FERMIAC</a> still exert subtle, unrecognized influence on approaches to computing in the modern era. They form chapters in an alternate history of computing, one not so dominated by the von Neumann architecture. Understanding them both denaturalizes the narrative that presents such machines as the inevitable "winners" of computer history and offers lessons for how to approach contemporary problems in computing from a different angle</p> 

      <canvas id="canvas" :height="height" :width="width" @click="canvasClick"></canvas>
      <div>
        <button @click="reset">Reset</button>
        <button @click="increaseSize">Increase Size</button>
      </div>
      <div id="vis">
        <div id="sumVis">
          <span class="node" v-for="s in sum_layer" :key="s.value"> {{s}} </span>
          <p>The sum layer totals the number of edges found in each column. An edge is the transition from a light area to a dark, and a dark area back out to the light area.</p>
        </div>

        <div id="convVis">
          <span class="node" v-for="c in conv_layer" :key="c.value"> {{c}} </span>
          <p>The difference/convolution layer takes the absolute value of each of the adjacent nodes in the sum layer. This is a way to capture information about horizontal boundaries using only the sum of edges detected columnwise. If there is an absolute difference in the number of edges between two adjacent nodes then there a shape has either begun or ended in the interim.</p>
        </div>

        <div id="consVis">
          <span class="singleNodeLayer"> {{cons_layer}} </span>
          <p>The consolidation layer simply sums the results of the difference/convolution layer.</p>
        </div>

        <div id="divLayer">
          <span class="singleNodeLayer"> {{div_layer}} </span>
          <p>Finally, the last layer divides the consolidation layer by 4, producing the number of shapes found on Numerete's surface</p>
        </div>

      </div>
  </div>
</template>

<script>


export default {
  name: 'numarete',
  props: {
    height: Number,
    width: Number
  },

  data () {
    return {
      numareteContext: null,
      canvas: null,
      penDown: false,
      startX: null,
      startY: null,
      inShape: false,
      polyPoints: [],
      completedPolys: [],

      sum_layer: null,
      conv_layer: null,
      cons_layer: 0,
      div_layer: 0
    }
  },
  created () {
    //construct net
    this.sum_layer = new Array(this.width).fill(0);
    this.conv_layer = new Array(this.width - 1).fill(0)
    
  },

  mounted () {
    this.canvas = document.getElementById("canvas");
    this.numareteContext = this.canvas.getContext("2d");
    this.numareteContext.translate(0.5, 0.5);
  },

  methods: {
    increaseSize(){
      this.width += 50
      this.height += 100
      this.canvas.width = this.width
      this.canvas.height = this.height
      this.reset()
    },

    canvasClick(e){
      if(!this.inshape){
        this.numareteContext.beginPath()
        //this.numareteContext.lineCap = 'round'
        this.numareteContext.strokeStyle = 'black'
        this.numareteContext.lineWidth = 1
        this.startX = e.offsetX
        this.startY = e.offsetY
        this.polyPoints.push([this.startX,this.startY])
        this.numareteContext.moveTo(this.startX, this.startY)
        this.inshape = true
      }
      else{
        if(Math.abs(e.offsetX-this.startX) <=4 && Math.abs(e.offsetY-this.startY) <=4){
          if(this.isConvex(this.polyPoints)){

            this.numareteContext.lineTo(this.startX, this.startY)
            this.numareteContext.stroke()
            this.numareteContext.fill()
            this.numareteContext.closePath()
            this.inshape = false
            this.startX = null
            this.startY = null
            this.completedPolys.push(this.polyPoints)
            this.polyPoints = []
            this.evalShapes()
          }
          else {

            this.numareteContext.closePath()
            this.numareteContext.clearRect(0,0,this.width,this.height)
            this.redrawCompleted()

            this.inshape = false
            this.startX = null
            this.startY = null
            this.polyPoints = []

          }
        }
        else{
          this.numareteContext.lineTo(e.offsetX, e.offsetY)
          this.polyPoints.push([e.offsetX, e.offsetY])
          this.numareteContext.stroke()
        }
      }


    },

    reset(){
      this.numareteContext.closePath()
      this.numareteContext.clearRect(0,0,this.width,this.height)
      this.sum_layer = new Array(this.width).fill(0);
      this.conv_layer = new Array(this.width - 1).fill(0)
      this.cons_layer = 0
      this.div_layer = 0

    },

    redrawCompleted(){
      if(this.completedPolys.length ==  0){return}

      for(var completedIndex in this.completedPolys){
         this.numareteContext.beginPath()
         for (var pointIndex in this.completedPolys[completedIndex]){
          if(pointIndex == 0) {this.numareteContext.moveTo(this.completedPolys[completedIndex][pointIndex][0], this.completedPolys[completedIndex][pointIndex][1])}
          else{
            this.numareteContext.lineTo(this.completedPolys[completedIndex][pointIndex][0], this.completedPolys[completedIndex][pointIndex][1])
            this.numareteContext.stroke()
          }
         }
         this.numareteContext.fill()
         this.numareteContext.closePath()

      }

    },


    evalShapes(){
        let rawImage = this.numareteContext.getImageData(0, 0, this.width, this.height);
        var binArray = Array(this.height).fill(0).map(x => Array(this.width).fill(0));

        //read the shape drawn onto the screen into an all or nothing form
        for(var y=0; y < this.height; y++){
            for(var x=0; x < this.width; x++) {
              var i = (x + (y * rawImage.width)) * 4;
              if(rawImage.data[i] != 0 || rawImage.data[i+1] != 0 || rawImage.data[i+2] != 0 || rawImage.data[i+3] != 0){
                  binArray[y][x] = 1;
              }
              else{
                  binArray[y][x] = 0;
              }
            }
        }

        //sum edges, apply to sum layer. Replace with original approach after getting essay.

        for(var sumNeuron in this.sum_layer){
          var group = []
          var groupNum = 0
          for(var rowtoCheck = 0; rowtoCheck < this.height; rowtoCheck++){
            if(binArray[rowtoCheck][sumNeuron] == 1){
              group.push(1)
            }
            else{
               group.push(0)
              }
            }
            var joinedGroup = group.join()
            var resplit = joinedGroup.split("0")
            console.log(resplit)
            for(var groupIndex in resplit){
              if(resplit[groupIndex].includes("1")){
                groupNum+=2
              }
            }
            //this.sum_layer[sumNeuron] = groupNum
            this.$set(this.sum_layer, sumNeuron, groupNum)

            console.log(this.sum_layer[sumNeuron])

          }
          


          for(var convInd=1; convInd < this.sum_layer.length; convInd++){
            //console.log(Math.abs(this.sum_layer[convInd].value - this.sum_layer[convInd - 1].value))

            //this.conv_layer[convInd-1] = Math.abs(this.sum_layer[convInd] - this.sum_layer[convInd - 1])
            this.$set(this.conv_layer, convInd - 1, Math.abs(this.sum_layer[convInd] - this.sum_layer[convInd - 1]))
          
         }


         console.log("conv",this.conv_layer)
         this.cons_layer = this.conv_layer.reduce((a, b) => a + b, 0)
         console.log("cons",this.cons_layer)
         this.div_layer =  Math.round(this.cons_layer/4)
         console.log("div",this.div_layer)


    },

  //Thanks to this Geekforgeek posting for the below convexity-detection code: https://www.geeksforgeeks.org/check-if-given-polygon-is-a-convex-polygon-or-not/
  CrossProduct(A)
  {

      var X1 = (A[1][0] - A[0][0]);
      var Y1 = (A[1][1] - A[0][1]);
      var X2 = (A[2][0] - A[0][0]);
      var Y2 = (A[2][1] - A[0][1]);

      return (X1 * Y2 - Y1 * X2);
  },
   

   isConvex(points)
  {

      var N = points.length;
      var prev = 0;
      var curr = 0;
   
      for (var i = 0; i < N; i++) {
          var temp= [ points[i],
                  points[(i + 1) % N],
                  points[(i + 2) % N] ];
   
          curr = this.CrossProduct(temp);
   
          if (curr != 0) {
   
              if (curr * prev < 0) {
                  return false;
              }
              else {
                  prev = curr;
              }
          }
      }
      return true;
  }

  }

}
</script>


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

#canvas {
    border: 3px solid black;
}

.node {
    border: 1px solid black;
    text-align: center;
    font-size: 6pt;
    padding: 2px;
}

.singleNodeLayer {
    border: 1px solid black;
    text-align: center;
    font-size: 12pt;
    padding: 2px;
}
</style>
