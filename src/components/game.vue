<template>
    <div>
        <div id="container">
            <canvas ref="game" id="game" width="100" height="100"></canvas>
        </div>

        <div id="controls" :class="{'toggled':!showRules}">
            <h2>Conway's Game of Life</h2>
            <div class="togglecontrols">
                <input type="checkbox" v-model="showRules" id="switch" />
                <label for="switch">Toggle</label>
            </div>
            <div v-if="showRules" class="rules">
            <p>The universe of the Game of Life is an infinite, two-dimensional orthogonal 
                grid of square cells, each of which is in one of two possible states, alive or dead, 
                (or populated and unpopulated, respectively).<br><br>
                Every cell interacts with its eight neighbours, which are the cells that are horizontally, 
                vertically, or diagonally adjacent. At each step in time, the following 
                transitions occur:</p>
            <ol>
                <li>Any live cell with fewer than two live neighbours dies, as if by underpopulation.</li>
                <li>Any live cell with two or three live neighbours lives on to the next generation.</li>
                <li>Any live cell with more than three live neighbours dies, as if by overpopulation.</li>
                <li>Any dead cell with exactly three live neighbours becomes a live cell, as if by reproduction.</li>
            </ol>
            <p>Press the start button and watch what can happen by applying this simple set of rules. 
                You also can draw your own patterns by clicking in the cells.</p>
            <!-- <div id="round">Step: <span>{{round}}</span></div> -->
            </div>
            <table id="buttons">
                <tr>
                    <td class="col1"><button id="run"  @click="run" type="button">{{ btn }}</button></td>
                    <td class="col2"><button id="step" @click="step" type="button">Step {{ round }}</button></td>
                </tr>
                <tr>
                    <td class="col1"><button id="clear" @click="clear" type="button">Clear</button></td>
                    <td class="col2"><button id="rand" @click="rand" type="button">Random Fill</button></td>
                </tr>
            </table>
        </div>
    </div>
</template>

<script>
import {Game} from '../game'
export default {
  data(){
    return {
        game:null,
        running:false,
        timer:undefined,
        btn:'Start',
        round:0,
        showRules:true
    }
  },
  mounted(){
        this.game = new Game(this.$refs.game, {cellColor:'#333', cellsX:160, cellsY:100, cellSize:8, gridColor:'#EFEFEF'})
        this.game.canvas.addEventListener("click", this.gameOnClick, false);
        this.game.randomize();

  },
  methods:{
      toggleControls:function(){
          this.showRules = !this.showRules;
      },
      run:function(){
            if(!this.running){
                this.timer = setInterval(() => {
                    this.game.step();
                    this.round += 1;
                }, 50);
            }
            else{
                this.timer = clearInterval(this.timer);
            }
            this.running = !this.running;
       
      },
      step:function(){
        this.running = false;
        this.timer = clearInterval(this.timer);
        this.game.step();
        this.round += 1;
      },
      clear:function(){
            this.running = false;
            this.timer = clearInterval(this.timer);
            this.game.clear();
            this.round = 0;
      },
      rand:function(){
          this.running = false;
          this.timer = clearInterval(this.timer);
          this.game.randomize();
          this.round = 0;
      },
        gameOnClick:function(e) {
            var x,y;
            
            //determine click position
            if (e.pageX !== undefined && e.pageY !== undefined) {
                x = e.pageX;
                y = e.pageY;
            } else {
                x = e.clientX + document.body.scrollLeft + document.documentElement.scrollLeft;
                y = e.clientY + document.body.scrollTop + document.documentElement.scrollTop;
            }
            
            // make it relativ to canvas
            x -= this.game.canvas.offsetLeft;
            y -= this.game.canvas.offsetTop;
            
            // calculate clicked cell
            x = Math.floor(x/this.game.cfg.cellSize);
            y = Math.floor(y/this.game.cfg.cellSize);
            
            this.game.toggleCell(x, y);
        }
  },
  computed:{

  },
  watch:{
      running:function(){
          if(this.running){
                this.btn = 'Stop';
            }
            else{
                this.btn = 'Start';
            }
      }

  }
    
}
</script>
<style scoped>

#container {
  position:absolute;
  background: #CCC;
  border:1px solid #CCC;
  width:100%;
}

#game {
    left:0;
    top:0;
    right:0;
    bottom:0;
    position: absolute;
}

.togglecontrols{
    position:absolute;
    right:20px;
    top:-5px;
}

input[type=checkbox]{
	height: 0;
	width: 0;
	visibility: hidden;
}

label {
	cursor: pointer;
	text-indent: -9999px;
	width: 40px;
	height: 20px;
	background: grey;
	display: block;
	border-radius: 20px;
	position: relative;
}

label:after {
	content: '';
	position: absolute;
	top: 0px;
	left: 0px;
	width: 20px;
	height: 20px;
	background: #fff;
	border-radius: 20px;
	transition: 0.3s;
}

input:checked + label {
	background: #9dd0ff;
}

input:checked + label:after {
	left: calc(100%);
	transform: translateX(-100%);
}

label:active:after {
	width: 40px;
}

#controls {
    width: 450px;
    padding:20px;
    position:absolute;
    right:15px;
    top:15px;
    background: rgba(40,40,40,.95);
	border: 1px solid #222;
    color:#FFF;
    box-shadow:2px 2px 5px rgba(0,0,0,.6);
}

#controls h2{
    font-size:14px;
    padding:0;
    margin:0;
}

#controls p{
    font-size:12px;
}

#controls ol{
    padding:0 0 0 15px;
}
#controls ol li{
    list-style-type:decimal;
    font-size:12px;
}

#round {
	padding: 5px;
}

.toggled{
    width:200px!important;
}

#buttons {
    width:100%;
}
#buttons td{
    width:50%;
}

.col1{
    padding:20px 10px 0 0;
}
.col2{
    padding:20px 0 0 10px;
}

#buttons button {
	width: 100%;
    background-color:#444;
    border:1px solid #FFF;
    padding:10px;
    margin:0;
    text-transform:none;
    color:#FFF;
    outline:none;
    font-size:12px;
}

.toggled #buttons button {
    padding:10px 2px;
    font-size:10px;
}
</style>
