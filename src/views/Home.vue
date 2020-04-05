<template>
  <div id="home">Microtonal Guitar Home</div>
</template>

<script>
var context = new AudioContext();
var oscillator = null;
var gainNode = context.createGain();
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
  var minFrequency = 20,
    maxFrequency = 2000;

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
  oscillator = context.createOscillator();
  oscillator.frequency.setTargetAtTime(freq, context.currentTime, 0.01);
  gainNode.gain.setTargetAtTime(gain, context.currentTime, 0.01);

  oscillator.connect(gainNode);
  gainNode.connect(context.destination);
  oscillator.start(context.currentTime);
}

function stop() {
  if (oscillator) {
    oscillator.stop(context.currentTime);
    oscillator.disconnect();
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
