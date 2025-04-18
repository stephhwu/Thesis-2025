<!-- SettingsSection.vue -->
<template>
  <div class="settings-container">
    <h1 class="settings-title">Unleash the Options</h1>
    
    <!-- First description outside the panel -->
    
    <div class="settings-panel">
      <div class="settings-panel-header">Who Let the Dogs In?</div>
      <p class="settings-top-description">You did. Use the slider below to decide how many dogs you want to see in this project.</p>

      
      <div class="slider-container">
        <div class="slider-label">I LIKE CATS</div>
        <input 
          type="range" 
          min="1" 
          max="10" 
          v-model="dogCount" 
          class="slider"
        />
        <div class="slider-label">DOGPILE ME</div>
      </div>
    </div>
    
    <div class="settings-panel">
      <div class="settings-panel-header">Fetching some guidance?</div>
      <p class="settings-description">Would you like a guide dog to accompany you through the project?</p>
      
      <div class="option-buttons">
        <label class="option-button">
          <input type="radio" v-model="needsGuide" :value="true" name="guide" @change="emitGuideSelection">
          <span class="button-box">
            <span v-if="needsGuide === true" class="x-mark">x</span>
          </span>
          <span class="button-label">YES</span>
        </label>
        
        <label class="option-button">
          <input type="radio" v-model="needsGuide" :value="false" name="guide" @change="emitGuideSelection">
          <span class="button-box">
            <span v-if="needsGuide === false" class="x-mark">x</span>
          </span>
          <span class="button-label">NO</span>
        </label>
      </div>
    </div>
    
    <!-- Replaced continue button with scroll indicator -->
    <div class="scroll-indicator" @click="onContinueClick">
      <span>Scroll down</span>
      <div class="arrow-down"></div>
    </div>
  </div>
</template>

<script>
import { gsap } from 'gsap';

export default {
  name: 'SettingsSection',
  data() {
    return {
      dogCount: 5,
      needsGuide: null
    }
  },
  mounted() {
    // Add the GSAP animation for the scroll indicator
    gsap.to(".scroll-indicator", {
      y: 10,
      repeat: -1,
      yoyo: true,
      duration: 1.5,
      delay: 5,
    });
  },
  methods: {
    emitGuideSelection() {
      console.log('Emitting guide selection:', this.needsGuide); // For debugging
      // Only emit if a choice has been made
      if (this.needsGuide !== null) {
        this.$emit('guide-selected', this.needsGuide);
      }
    },
    onContinueClick() {
      this.emitGuideSelection();
      this.$emit('continue-clicked');
    }
  }
}
</script>

<style scoped>
.settings-container {
  width: 100%;
  min-height: 100vh;
  background: #f2efea;
  padding: 40px 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: "ivypresto-headline", serif;
  font-weight: 400;
  font-style: normal;
  position: relative; /* Added to position the scroll indicator */
}

.settings-title {
  color: red;
  font-family: "ivypresto-headline", serif;
  margin-top: 70px;
  font-weight: 400;
  font-style: normal;
  font-size: 64px;
  margin-bottom: 60px;
  text-align: center;
  perspective: 500px;
}

/* Individual character styling for animation */
.settings-title .char {
  display: inline-block;
  white-space: pre;
}

.settings-top-description {
  font-size: 18px;
  font-family: "neue-haas-grotesk-display", sans-serif;
  font-weight: 400;
  font-style: normal;
  text-align: center;
  margin-bottom: 30px;
  color: #333;
  width: 100%;
  position: relative;
}

.settings-panel {
  width: 100%;
  max-width: 800px;
  margin-bottom: 60px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.settings-panel-header {
  background-color: #81C9E3;
  padding: 10px 20px;
  font-size: 24px;
  margin-bottom: 20px;
  text-align: center;
}

.settings-description {
  font-size: 18px;
  font-family: "neue-haas-grotesk-display", sans-serif;
  font-weight: 400;
  font-style: normal;
  text-align: center;
  margin-bottom: 30px;
  color: #333;
  width: 100%;
  position: relative;
}

.slider-container {
  display: flex;
  align-items: center;
  width: 100%;
  justify-content: space-between;
  margin-top: 10px;
  position: relative;
}

.slider-label {
  font-weight: bold;
  font-size: 18px;
  width: 115px;
  text-align: center;
  font-family: "roca", sans-serif;
  font-weight: 400;
  font-style: normal;
}

.slider {
  flex: 1;
  height: 8px;
  appearance: none;
  background: #ddd;
  outline: none;
  margin: 0 20px;
  border-radius: 4px;
}

.slider::-webkit-slider-thumb {
  appearance: none;
  width: 25px;
  height: 25px;
  background: red;
  border-radius: 50%;
  cursor: pointer;
}

.option-buttons {
  display: flex;
  justify-content: center;
  gap: 60px;
  margin-top: 10px;
  width: 100%;
  position: relative;
}

.option-button input {
  display: none;
}

.button-box {
  width: 50px;
  height: 50px;
  border: 2px solid #333;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 10px;
  border-radius: 10px;
}

.x-mark {
  color: red;
  font-size: 24px;
  font-weight: bold;
}

.button-label {
  font-size: 20px;
  width: 50px;
  font-weight: bold;
  font-family: "roca", sans-serif;
  font-weight: 400;
  font-style: normal;
  text-align: center;
}

/* Remove the continue button styles */
/* .continue-button { ... } */

/* Add the scroll indicator styles */
.scroll-indicator {
  position: absolute;
  bottom: 40px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  cursor: pointer;
  color: red;
  font-family: "ivypresto-headline", serif;
  opacity: 0;
  animation: fadeIn 1s ease-in-out 5s forwards;
}

.scroll-indicator span {
  margin-bottom: 10px;
}

.arrow-down {
  width: 20px;
  height: 20px;
  border-left: 2px solid red;
  border-bottom: 2px solid red;
  transform: rotate(-45deg);
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>