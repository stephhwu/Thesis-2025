<template>
  <div class="dog-guide-container">
    <div id="leash"></div>
    <svg id="dog" viewBox="0 0 100 60">
      <!-- Dog body -->
      <ellipse cx="50" cy="35" rx="30" ry="15" fill="#c87137"/>
      <!-- Dog head -->
      <circle cx="20" cy="30" r="12" fill="#d47d3b"/>
      <!-- Dog ear 1 -->
      <ellipse cx="15" cy="22" rx="6" ry="8" fill="#b05e1a"/>
      <!-- Dog ear 2 -->
      <ellipse cx="15" cy="22" rx="4" ry="6" fill="#d47d3b"/>
      <!-- Dog nose -->
      <ellipse cx="12" cy="32" rx="4" ry="3" fill="#663300"/>
      <!-- Dog eye -->
      <circle cx="17" cy="27" r="2" fill="#000000"/>
      <!-- Dog tail -->
      <path d="M80,30 Q90,20 85,10" stroke="#c87137" stroke-width="5" fill="none"/>
      <!-- Dog legs -->
      <rect x="35" y="45" width="5" height="10" fill="#a35c1d"/>
      <rect x="60" y="45" width="5" height="10" fill="#a35c1d"/>
      <rect x="45" y="45" width="5" height="12" fill="#a35c1d"/>
      <rect x="70" y="45" width="5" height="12" fill="#a35c1d"/>
    </svg>
    <div id="cursor"></div>
  </div>
</template>

<script>
export default {
  name: 'DogGuide',
  mounted() {
    this.initDogSimulation();
  },
  beforeUnmount() {
    // Clean up event listeners when component is removed
    document.removeEventListener('mousemove', this.handleMouseMove);
    cancelAnimationFrame(this.animationFrame);
  },
  data() {
    return {
      dog: null,
      leash: null,
      cursor: null,
      dogX: 0,
      dogY: 0,
      mouseX: 0,
      mouseY: 0,
      dogSpeed: 0,
      maxSpeed: 5,
      acceleration: 0.2,
      deceleration: 0.1,
      isMoving: false,
      lastMouseX: 0,
      lastMouseY: 0,
      dogDirection: 0,
      animationFrame: null
    };
  },
  methods: {
    initDogSimulation() {
      this.dog = document.getElementById('dog');
      this.leash = document.getElementById('leash');
      this.cursor = document.getElementById('cursor');
      
      // Dog's initial position
      this.dogX = window.innerWidth / 2;
      this.dogY = window.innerHeight * 0.7;
      
      // Mouse position
      this.mouseX = window.innerWidth / 2;
      this.mouseY = window.innerHeight / 2;
      this.lastMouseX = this.mouseX;
      this.lastMouseY = this.mouseY;
      
      // Update cursor position on mouse move
      this.handleMouseMove = (e) => {
        this.mouseX = e.clientX;
        this.mouseY = e.clientY;
        
        // Check if mouse is moving
        if (Math.abs(this.mouseX - this.lastMouseX) > 1 || 
            Math.abs(this.mouseY - this.lastMouseY) > 1) {
          this.isMoving = true;
        } else {
          this.isMoving = false;
        }
        
        this.lastMouseX = this.mouseX;
        this.lastMouseY = this.mouseY;
      };
      
      document.addEventListener('mousemove', this.handleMouseMove);
      
      // Initial dog positioning
      this.dog.style.left = (this.dogX - 30) + 'px';
      this.dog.style.top = (this.dogY - 20) + 'px';
      
      // Start animation
      this.animate();
    },
    
    animate() {
      // Update cursor position
      this.cursor.style.left = this.mouseX + 'px';
      this.cursor.style.top = this.mouseY + 'px';
      
      // Calculate distance between dog and mouse
      const dx = this.mouseX - this.dogX;
      const dy = this.mouseY - this.dogY;
      const distance = Math.sqrt(dx * dx + dy * dy);
      
      // Calculate angle for dog direction
      this.dogDirection = Math.atan2(dy, dx);
      
      // If mouse is moving, accelerate dog
      if (this.isMoving && distance > 50) {
        this.dogSpeed = Math.min(this.dogSpeed + this.acceleration, this.maxSpeed);
      } else {
        // Decelerate dog if mouse stops or is close enough
        this.dogSpeed = Math.max(this.dogSpeed - this.deceleration, 0);
      }
      
      // Move dog if it has speed
      if (this.dogSpeed > 0.1) {
        this.dogX += Math.cos(this.dogDirection) * this.dogSpeed;
        this.dogY += Math.sin(this.dogDirection) * this.dogSpeed;
        
        // Flip dog sprite based on direction
        if (dx > 0) {
          this.dog.style.transform = 'scaleX(1)';
        } else {
          this.dog.style.transform = 'scaleX(-1)';
        }
      }
      
      // Position dog
      this.dog.style.left = (this.dogX - 30) + 'px';
      this.dog.style.top = (this.dogY - 20) + 'px';
      
      // Draw leash
      const leashLength = Math.sqrt(
        Math.pow(this.mouseX - this.dogX, 2) + Math.pow(this.mouseY - this.dogY, 2)
      );
      
      this.leash.style.width = leashLength + 'px';
      this.leash.style.left = this.dogX + 'px';
      this.leash.style.top = this.dogY + 'px';
      
      const leashAngle = Math.atan2(this.mouseY - this.dogY, this.mouseX - this.dogX) * 180 / Math.PI;
      this.leash.style.transform = `rotate(${leashAngle}deg)`;
      
      this.animationFrame = requestAnimationFrame(this.animate);
    }
  }
}
</script>

<style scoped>

.dog-guide-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1000;
  pointer-events: none;
}

#dog {
  position: absolute;
  width: 60px;
  height: 40px;
  transition: transform 0.1s ease;
  pointer-events: none;
}

#leash {
  position: absolute;
  height: 2px;
  background-color: #F9D74A;
  transform-origin: 0% 0%;
  pointer-events: none;
}

#cursor {
  position: absolute;
  width: 20px;
  height: 20px;
  background-color: #80AF39;
  border-radius: 50%;
  transform: translate(-50%, -50%);
  z-index: 100;
  pointer-events: none;
}
</style>