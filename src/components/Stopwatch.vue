<template>
   <div class="container">
      <h1>{{ msg }}</h1>

      <div class="stopwatch"></div>

      <button class="button" @click="start()">Start</button>
      <button class="button" @click="stop()">Stop</button>
      <button class="button" @click="restart()">Reset</button>
      <button class="button" @click="lap()">Lap</button>
      <button class="button" @click="clear()">Clear Laps</button>
		
	<ul class="results"></ul>
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
      }
   },
   methods: {
      reset: function() { 
         this.times = [ 0, 0, 0];
      },
    
      start: function() {
         if (!this.time) this.time = performance.now();
         
         
         if (!this.running) {
            
            this.running = true;
            
            requestAnimationFrame(this.step.bind(this));
         }
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
      },

      restart: function() {
        this.stop();
        this.reset();
        this.print(this.times);
        
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
  position: fixed;
  top: 1em;
  width: 100%;
}

.button {
  cursor: pointer;
  margin: 0 20px;
  font-size: 20px;
  border-radius: 50%;
  display: inline-flex;
  width: 70px;
  height: 70px;
}

.button:first-child {
    margin-left: 0;
}

.button:last-child {
    margin-right: 0;
}

.button:hover {
  color: white;
}

.stopwatch {
  font-size: 50px;
  text-align: center;
  margin-bottom: 30px;
  color: #555
}

.results {
  border-color: lime;
  list-style: none;
  margin: 0;
  padding: 0;
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
}

</style>
