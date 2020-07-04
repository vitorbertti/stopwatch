<template>
   <div class="container">
      <h1>{{ msg }}</h1>

      <label id="timerLabel" for="timer" hidden>Timer in minutes</label>
      <select id="timer" v-model="timer" @change="setTimer()" hidden>
         <option v-for="index in 60" :key="index" v-bind:timer="timer" >{{ index }}</option>
      </select>

      <div id="time" class="stopwatch"></div>

      <button id="start" class="button start" @click="start()" >Start</button>
      <button id="stop" class="button stop" @click="stop()">Stop</button>
      <button id="restart" class="button reset" @click="restart()">Reset</button>
      <button id="lap" class="button lap" @click="lap()">Lap</button>
      <button id="clear" class="button clear" @click="clear()">Clear</button>
      <button id="timerDisplay" class="button timer" @click="setTimerDisplay()">Timer</button>

      <div class="laps">
         <ul class="results"></ul>
      </div>
	
   </div>
</template>

<script>
export default {
   name: 'Stopwatch',
   props: {
      msg: String,
   },
   data: function () {
      return {
         running: false,
         display: null,
         results: null,
         times: [],
         laps: [],
         timer: [],
         timerDisplay: false,
      }
   },
   methods: {
      reset: function() { 
         this.times = [ 0, 0, 0 ];
         this.timer = [];
      },
    
      start: function() {    
         if(this.timer.length) {
            const show = document.querySelector('#time');
            this.startTimer(this.timer, show);
            return;
         }

         else if (!this.time) {
            this.time = performance.now();
         }
         
         if (!this.running) {
            this.running = true;
            requestAnimationFrame(this.step.bind(this));
         }
      },

      setTimer: function() { 
         const unformatted = this.format( [ '0' , this.timer ] ).trim().split(':');
         const formatted = `${unformatted[0]}: ${unformatted[1]}: 00`;
         this.display.innerText = formatted;
      },

      startTimer: function(time, display) { 
         
         const duration = time * 60;
         let timer = duration, minutes, seconds;
         let execution = false;
         setInterval(function () {
            if(execution) return;   
            
            minutes = parseInt(timer / 60, 10);
            seconds = parseInt(timer % 60, 10);

            minutes = minutes < 10 ? "0" + minutes : minutes;
            seconds = seconds < 10 ? "0" + seconds : seconds;

            display.textContent = "00: " + minutes + ": " + seconds;

            if (--timer < 0) {
               execution = true;
            }
         }, 1000);
      },
    
      lap: function() {
         let times = this.times;
         let li = document.createElement('li');
         li.innerText = this.format(times);
         this.results.appendChild(li);
      },
    
      stop: function() {
         this.running = false;
         this.time = null;
         this.pause = true;
         this.timer = [];
         const show = document.querySelector('#time');
         this.startTimer(this.timer, show);
         return;    
      },

      restart: function() {
        this.stop();
        this.reset();
        this.print();
        
      },
    
      clear: function() {
         this.clearChildren(this.results);
      },
    
      step: function(timestamp) {   
         if (!this.running) {
            return;
         }
         this.calculate(timestamp);
         this.time = timestamp;
         this.print();
         requestAnimationFrame(this.step.bind(this));
      },
    
      calculate: function(timestamp) {
         var diff = timestamp - this.time;
         this.times[2] += diff / 10;
         if (this.times[2] >= 100) {
            this.times[1] += 1;
            this.times[2] -= 100;
         }
        
         if (this.times[1] >= 60) {
            this.times[0] += 1;
            this.times[1] -= 60;
         }
      },
    
      print: function() {
         this.display.innerText = this.format(this.times);
      },
    
      format: function(times) {
         return `\
            ${this.pad0(times[0], 2)}:\
            ${this.pad0(times[1], 2)}:\
            ${this.pad0(Math.floor(times[2]), 2)}`;
      },

      pad0: function(value, count) {
         var result = value.toString();
         for (; result.length < count; --count)
            result = '0' + result;
         return result;
      },

      clearChildren: function(node) {
        while (node.lastChild)
           node.removeChild(node.lastChild);
      },

      setTimerDisplay: function() {
         if(!this.timerDisplay) {
            document.querySelector('#timerLabel').hidden = false;
            document.querySelector('#timer').hidden = false;

            document.querySelector('#stop').style.display = "none";
            document.querySelector('#restart').style.display = "none";
            document.querySelector('#lap').style.display = "none";
            document.querySelector('#clear').style.display = "none";
            document.querySelector('#timerDisplay').innerHTML = 'Back';
            this.timerDisplay = !this.timerDisplay;
         }else{
            document.querySelector('#timerLabel').hidden = true;
            document.querySelector('#timer').hidden = true;

            document.querySelector('#stop').style.display = "inline";
            document.querySelector('#restart').style.display = "inline";
            document.querySelector('#lap').style.display = "inline";
            document.querySelector('#clear').style.display = "inline";
            document.querySelector('#timerDisplay').innerHTML = 'Timer';
            this.timerDisplay = !this.timerDisplay;
         }
        
      }
   },
      
   mounted: function () { 
      this.display = document.querySelector('.stopwatch');
      this.results = document.querySelector('.results');
      this.reset();
      this.print(this.times);
   },
   
};
</script>

<style scoped>

.container {
  padding: 20px;
  text-align: center;
  justify-items: center;
  top: 1em;
}

.button {
  cursor: pointer;
  margin: 0 20px;
  font-size: 20px;
  border-radius: 50%;
  display: inline-flex;
  justify-content: center;
  align-items: center;
  width: 70px;
  height: 70px;
  color: #fff;
}

.start {
   background-color: #4caf50;
}

.start:hover {
   background-color: #81c784;
}

.stop {
   background-color: #f44336;
}

.stop:hover {
   background-color: #e57373;
}

.reset {
   background-color: #2196f3;
}

.reset:hover {
   background-color: #64b5f6;
}

.lap {
   background-color: #e07d2c;
}

.lap:hover {
   background-color: #e09150;
}

.clear {
   background-color: #634b48;
}

.clear:hover {
   background-color: #5f5c5b;
}

.timer {
   background-color: #000000;
}

.timer:hover {
   background-color: #222222;
}

.stopwatch {
  font-size: 50px;
  text-align: center;
  margin-bottom: 60px;
  color: #555
}

.laps {
   display: flex;
   flex-direction: column;
   align-content: center;
   justify-content: center;
   width: 280px;
   margin: auto;
}

.results {
  list-style: none;
  font-size: 30px
}

</style>
