<template>
  <div class="timeline-container">
    <div class="title-container">
      <h1 class="timeline-title">Dog Domestication</h1>
      <p class="timeline-subtitle">
        <span>Canis lupus: grey wolf</span> 
        <span class="arrow">&#10230;</span> 
        <span>Canis familiaris: dog</span>
      </p>
    </div>
    
    <!-- Timeline track with drag instructions -->
    <div class="timeline-track">
      <div class="drag-instructions">Drag the marker to navigate</div>
    </div>
    
    <!-- Draggable marker with handle -->
    <div class="marker-wrapper" ref="markerWrapper">
      <div class="marker">
        <div class="grab-handle">
          <div class="drag-line"></div>
          <div class="drag-line"></div>
          <div class="drag-line"></div>
        </div>
      </div>
      <div class="timeline-year">{{ currentYear }}</div>
    </div>



    <div class="slider">
      <div class="slider-wrapper" ref="sliderWrapper">
        <div class="slide" v-for="(item, i) in timelineItems" :key="i">
          <img :src="item.image" :alt="item.title" />
          <div class="slide-caption">{{ item.caption }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { onMounted, ref, onBeforeUnmount, computed } from 'vue';
import { gsap } from 'gsap';
import { Draggable } from 'gsap/Draggable';

// Register the Draggable plugin
gsap.registerPlugin(Draggable);

export default {
  name: 'TimelineSection',
  props: {
    isUnlocked: {
      type: Boolean,
      default: false
    }
  },
  setup(props, { emit }) {
    const sliderWrapper = ref(null);
    const markerWrapper = ref(null);
    let target = 0;
    let current = 0;
    let ease = 0.075;
    let maxScroll = 0;
    let animationFrame = null;
    let resizeListener = null;
    let draggableInstance = null;
    const currentIndex = ref(0);
    
    // Timeline data with years and captions
    const timelineItems = [
      { 
        year: '40,000 BCE', 
        image: '/src/assets/images/dog-1.png', 
        caption: 'The timing and location of the first dog domesticated but it likely occurred between 14,000 & 40,000 years ago' 
      },
      { 
        year: '15,000 BCE', 
        image: '/src/assets/images/dog-2.png', 
        caption: 'Early dogs were used for hunting and protection' 
      },
      { 
        year: '1700s', 
        image: '/src/assets/images/dog-3.png', 
        caption: 'Until the late 1800s dogs were bred primarily for work' 
      },
      { 
        year: '1800s', 
        image: '/src/assets/images/dog-4.png', 
        caption: 'Dogs were then bred for status' 
      },
      { 
        year: '1900s', 
        image: '/src/assets/images/dog-5.png', 
        caption: 'Modern dog breeds developed' 
      },
      { 
        year: '1950s', 
        image: '/src/assets/images/dog-6.png', 
        caption: 'Dogs become common family pets' 
      },
      { 
        year: '1980s', 
        image: '/src/assets/images/dog-7.png', 
        caption: 'Service dogs gain recognition' 
      },
      { 
        year: '2000s', 
        image: '/src/assets/images/dog-8.png', 
        caption: 'Designer mixed breeds become popular' 
      },
      { 
        year: '2010s', 
        image: '/src/assets/images/dog-9.png', 
        caption: 'DNA testing reveals ancestry' 
      },
      { 
        year: 'Present', 
        image: '/src/assets/images/dog-10.png', 
        caption: 'Dogs continue to evolve alongside humans' 
      }
    ];

    // Computed property for current year based on index
    const currentYear = computed(() => {
      return timelineItems[currentIndex.value]?.year || '';
    });
    
    // Track if we're at the beginning of the timeline
    const isAtStart = computed(() => {
      return currentIndex.value === 0;
    });

    // Track if we're at the end of the timeline
    const isAtEnd = computed(() => {
      return currentIndex.value === timelineItems.length - 1;
    });

    function lerp(start, end, factor) {
      return start + (end - start) * factor;
    }

    // Update the timeline based on marker position
    function updateTimelineFromMarkerPosition(markerX) {
      // Calculate which timeline item we're closest to
      const trackWidth = window.innerWidth - 200; // Account for marker width and padding
      const progress = (markerX - 70) / trackWidth;
      const index = Math.min(
        timelineItems.length - 1,
        Math.max(0, Math.floor(progress * timelineItems.length))
      );
      
      currentIndex.value = index;
      
      // Calculate target position for the slider
      const slideWidth = 600; // Width of slide + gap
      target = index * slideWidth;
    }

    // Move the timeline by a specific number of items
    function moveTimeline(direction) {
      const newIndex = Math.min(
        timelineItems.length - 1,
        Math.max(0, currentIndex.value + direction)
      );
      
      if (newIndex !== currentIndex.value) {
        currentIndex.value = newIndex;
        
        // Calculate new marker position
        const trackWidth = window.innerWidth - 200;
        const markerPosition = 70 + (trackWidth * newIndex / (timelineItems.length - 1));
        
        // Animate the marker
        gsap.to(markerWrapper.value, {
          x: markerPosition,
          duration: 0.5,
          ease: "power2.out"
        });
        
        // Calculate target position for the slider
        const slideWidth = 600; // Width of slide + gap
        target = newIndex * slideWidth;
      }
    }

    function update() {
      // Smooth the animation
      current = lerp(current, target, ease);
      
      // Update slider position
      if (sliderWrapper.value) {
        gsap.set(sliderWrapper.value, {
          x: -current
        });
      }
      
      animationFrame = requestAnimationFrame(update);
    }

    // Set up draggable marker
    function setupDraggable() {
      if (!markerWrapper.value) return;
      
      // Calculate bounds for the marker
      const trackWidth = window.innerWidth - 200;
      
      // Create the draggable instance
      draggableInstance = Draggable.create(markerWrapper.value, {
        type: "x",
        bounds: { minX: 70, maxX: trackWidth + 70 },
        edgeResistance: 0.9,
        onDrag: function() {
          updateTimelineFromMarkerPosition(this.x);
        },
        onDragEnd: function() {
          // Snap to the nearest timeline item
          const trackWidth = window.innerWidth - 200;
          const markerPosition = 70 + (trackWidth * currentIndex.value / (timelineItems.length - 1));
          
          gsap.to(markerWrapper.value, {
            x: markerPosition,
            duration: 0.3,
            ease: "power2.out"
          });
        }
      })[0];
      
      // Position marker at first item
      gsap.set(markerWrapper.value, { x: 70 });
    }

    onMounted(() => {
      // Start animation loop
      update();
      
      // Set up the draggable marker
      setupDraggable();
      
      // Add resize listener
      resizeListener = () => {
        // Recalculate dimensions
        maxScroll = sliderWrapper.value.offsetWidth - window.innerWidth;
        
        // Update draggable bounds if instance exists
        if (draggableInstance) {
          const trackWidth = window.innerWidth - 200;
          draggableInstance.applyBounds({ minX: 70, maxX: trackWidth + 70 });
          
          // Reposition marker based on current index
          const markerPosition = 70 + (trackWidth * currentIndex.value / (timelineItems.length - 1));
          gsap.set(markerWrapper.value, { x: markerPosition });
        }
      };
      
      window.addEventListener("resize", resizeListener);
      
      // Add title animation
      const timelineTitle = document.querySelector('.timeline-title');
      if (timelineTitle) {
        const titleText = timelineTitle.textContent;
        let titleHTML = "";
        
        for (let i = 0; i < titleText.length; i++) {
          titleHTML += `<span class="char">${titleText[i]}</span>`;
        }
        
        timelineTitle.innerHTML = titleHTML;
        
        gsap.from(".timeline-title .char", {
          opacity: 0,
          y: 100,
          rotateX: -90,
          stagger: 0.02,
          ease: "back.out(1.7)",
          duration: 1
        });
      }
      
      // Show drag instructions briefly
      gsap.to(".drag-instructions", {
        opacity: 1,
        duration: 1,
        delay: 0.5,
        onComplete: () => {
          gsap.to(".drag-instructions", {
            opacity: 0,
            duration: 1,
            delay: 3
          });
        }
      });
    });
    
    onBeforeUnmount(() => {
      window.removeEventListener("resize", resizeListener);
      cancelAnimationFrame(animationFrame);
      
      // Kill draggable instance
      if (draggableInstance) {
        draggableInstance.kill();
        draggableInstance = null;
      }
    });

    return {
      sliderWrapper,
      markerWrapper,
      timelineItems,
      currentYear,
      isAtStart,
      isAtEnd,
      moveTimeline,
      currentIndex
    };
  }
};
</script>

<style scoped>
.timeline-container {
  width: 100%;
  height: 100vh;
  position: relative;
  overflow: hidden;
}

.title-container {
  position: absolute;
  top: 40px;
  right: 60px;
  text-align: right;
  z-index: 20;
}

.timeline-title {
  color: red;
  font-family: "ivypresto-headline", serif;
  font-weight: 400;
  font-style: normal;
  font-size: 55px;
  margin-bottom: 10px;
}

.timeline-subtitle {
  font-family: "ivypresto-headline", serif;
  font-weight: 100;
  font-style: italic;
  font-size: 18px;
  color: red;
  margin-top: 15px;
  text-align: right;
}

.timeline-subtitle .arrow {
  margin: 0 10px;
  display: inline-block;
}

/* Timeline track for visual guidance */
.timeline-track {
  position: absolute;
  top: 140px;
  left: 0;
  width: 100%;
  height: 2px;
  background-color: rgba(255, 0, 0, 0.3);
  z-index: 5;
}

/* Drag instructions tooltip */
.drag-instructions {
  position: absolute;
  top: -40px;
  left: 11%;
  transform: translateX(-50%);
  background-color: rgba(255,0,0,0.8);
  color: white;
  padding: 5px 12px;
  border-radius: 15px;
  font-size: 14px;
  opacity: 0;
  white-space: nowrap;
}

/* Individual character styling for animation */
.timeline-title .char {
  display: inline-block;
  white-space: pre;
}

.slider {
  width: 100%;
  height: 70vh;
  overflow: hidden;
  margin-top: 180px; /* Push content down to make room for the timeline */
}

.slider-wrapper {
  width: max-content;
  padding: 0 150px;
  height: 100%;
  display: flex;
  align-items: center;
  gap: 100px;
}

.slide {
  width: 500px;
  height: 500px !important;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.slide img {
  width: 100%;
  height: 85%;
  object-fit: cover;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}

.slide-caption {
  margin-top: 20px;
  font-family: "roca", sans-serif;
  font-weight: 400;
  font-size: 18px;
  color: #333;
  max-width: 100%;
  text-align: center;
}

.marker-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  width: max-content;
  height: 100vh;
  z-index: 10;
  cursor: grab;
}

.marker-wrapper:active {
  cursor: grabbing;
}

.marker {
  position: relative;
  width: 2px;
  height: 100%;
  background: red;
}

.marker:after {
  position: absolute;
  content: "";
  display: block;
  top: 120px; /* Moved higher to avoid covering images */
  left: -20px;
  width: 40px;
  height: 40px;
  background-color: #F2EFEB;
  border: 2px solid red;
  border-radius: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: grab;
}

.grab-handle {
  position: absolute;
  top: 120px;
  left: -20px;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  z-index: 12;
  cursor: grab;
}

.grab-handle:active {
  cursor: grabbing;
}

.drag-line {
  width: 20px;
  height: 2px;
  background-color: rgba(255, 0, 0, 0.7);
  margin: 2px 0;
}

.timeline-year {
  position: absolute;
  top: 170px; /* Moved below the circle */
  left: 0;
  width: 100px;
  font-family: "ivypresto-headline", serif;
  font-size: 18px;
  color: #000;
  text-align: center;
  transform: translateX(-50%);
  margin-left: 70px;
}


.nav-btn {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: rgba(255, 0, 0, 0.8);
  color: white;
  border: none;
  font-size: 18px;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: background-color 0.2s;
}

.nav-btn:hover {
  background-color: rgba(255, 0, 0, 1);
}

.nav-btn:disabled {
  background-color: rgba(200, 200, 200, 0.5);
  cursor: not-allowed;
}
</style>