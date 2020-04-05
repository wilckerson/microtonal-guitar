<template>
  <div id="home">Microtonal Guitar Home</div>
</template>

<script>
var context = new AudioContext();
var oscillator = context.createOscillator();
var gainNode = context.createGain();
  

 oscillator.connect(gainNode);
  gainNode.connect(context.destination);

  //oscillator.connect(context.destination);
   gainNode.gain.value = 0;

   //gainNode.gain.setTargetAtTime(0, context.currentTime,0.1);
  oscillator.start(context.currentTime+1);


var isPressed = false;

function init() {
  if (isTouchDevice()) {
    document.body.addEventListener("touchstart", function(e) {
      
      onPress(e.touches[0].clientX, e.touches[0].clientY);
    });
    document.body.addEventListener("touchend", function() {
      onRelease();
    });

    document.body.addEventListener("touchmove", function(e) {
      onMove(e.touches[0].clientX, e.touches[0].clientY);
    });
  } else {
    document.body.addEventListener("mousedown", function(e) {
      onPress(e.clientX, e.clientY);
    });
    document.body.addEventListener("mouseup", function() {
      onRelease();
    });

    document.body.addEventListener("mousemove", function(e) {
      onMove(e.clientX, e.clientY);
    });
  }
}

function isTouchDevice() {
  var touchDevice =
    navigator.maxTouchPoints || "ontouchstart" in document.documentElement;

  return touchDevice;
}

function calculateFrequency(mouseXPosition) {
  var minFrequency = 100,
    maxFrequency = 800;

  return (mouseXPosition / window.innerWidth) * maxFrequency + minFrequency;
}

function calculateGain(mouseYPosition) {
  var minGain = 0,
    maxGain = 1;

  return 1 - (mouseYPosition / window.innerHeight) * maxGain + minGain;
}

function changeSound(freq, gain) {
  if (isPressed && oscillator) {
    oscillator.frequency.setTargetAtTime(freq, context.currentTime, 0.01);
    gainNode.gain.setTargetAtTime(gain, context.currentTime, 0.01);

  }
}

function onPress(x, y) {
  isPressed = true;
  var freq = calculateFrequency(x);
  var gain = calculateGain(y);
  play(freq, gain);
}

function onRelease() {
  isPressed = false;
  stop();
}

function onMove(x, y) {
  var freq = calculateFrequency(x);
  var gain = calculateGain(y);
  changeSound(freq, gain);
}

function play(freq, gain) {
 
 //alert('debug: '+gain)
  oscillator.frequency.setTargetAtTime(freq, context.currentTime, 0.01);
  //gain = 1;
  gainNode.gain.setTargetAtTime(gain, context.currentTime, 0.01);

 
  //oscillator.start(context.currentTime);
}

function stop() {
  if (oscillator) {
  gainNode.gain.setTargetAtTime(0, context.currentTime, 0.01);

    //oscillator.stop(context.currentTime);
    //oscillator.disconnect();
  }
}

export default {
  mounted() {
    init();
  }
};
</script>

<style scoped>
#home {
  height: 100vh;
  background-color: red;
  user-select: none;
}
</style>
