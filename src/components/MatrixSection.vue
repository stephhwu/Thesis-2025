<template>
  <div class="matrix-container">
    <div class="fixed-container" ref="fixedContainer">
      <h1 class="header">Some Cool Dog Jobs</h1>
      <div class="subtext">subtext about how cool dog jobs are</div>
      <div class="instruction">scroll to explore</div>
      
      <!-- Category Labels -->
      <div class="category-label" id="sniffers" ref="sniffersLabel">sniffers</div>
      <div class="category-label" id="guides" ref="guidesLabel">guides</div>
      <div class="category-label" id="medical" ref="medicalLabel">medical</div>
      <div class="category-label" id="misc" ref="miscLabel">misc</div>
      
      <!-- Axes -->
      <div class="axis horizontal-axis" ref="horizontalAxis"></div>
      <div class="axis vertical-axis" ref="verticalAxis"></div>
      
      <!-- Highlighted Job Popup -->
      <div class="highlighted-job" ref="highlightedJob">
        <h3>lantern fly hunting</h3>
        <p>Novel canine scent detection program holds promise in PA's fight against Spotted Lanternfly</p>
        <span class="learn-more">learn more →</span>
      </div>
      
      <!-- Circles will be rendered here using v-for instead of DOM manipulation -->
      <div 
        v-for="(circle, index) in circles" 
        :key="`circle-${index}`"
        :id="`circle-${circle.id}`"
        class="circle" 
        :data-category="circle.category"
        :style="getCircleStyles(circle)"
        @mouseenter="showJobDetails(circle, $event)" 
        @mouseleave="hideJobDetails"
      ></div>
    </div>
  </div>
</template>

<script>
import { onMounted, onBeforeUnmount, ref, reactive, computed } from 'vue';
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

gsap.registerPlugin(ScrollTrigger);

export default {
  name: 'MatrixSection',
  setup() {
    const fixedContainer = ref(null);
    const horizontalAxis = ref(null);
    const verticalAxis = ref(null);
    const highlightedJob = ref(null);
    const sniffersLabel = ref(null);
    const guidesLabel = ref(null);
    const medicalLabel = ref(null);
    const miscLabel = ref(null);
    
    const circles = ref([]);
    const scrollProgress = ref(0);
    const scrollTrigger = ref(null);
    
    // Configuration
    const numCircles = 32; // 8 per quadrant

    // Define dog jobs by category to ensure balanced distribution
    const dogJobs = {
      sniffers: [
        { name: "lantern fly hunting", description: "Novel canine scent detection program holds promise in PA's fight against Spotted Lanternfly" },
        { name: "drug detection" },
        { name: "search and rescue" },
        { name: "truffle hunting" },
        { name: "bomb detection" },
        { name: "cadaver detection" },
        { name: "termite detection" },
        { name: "wildlife tracking" }
      ],
      guides: [
        { name: "guide dogs" },
        { name: "therapy dogs" },
        { name: "service dogs" },
        { name: "emotional support" },
        { name: "mobility assistance" },
        { name: "hearing dogs" },
        { name: "autism support" },
        { name: "PTSD support" }
      ],
      medical: [
        { name: "seizure alert" },
        { name: "diabetes alert" },
        { name: "cancer detection" },
        { name: "blood pressure alert" },
        { name: "migraine alert" },
        { name: "allergy detection" },
        { name: "hospital therapy" },
        { name: "rehabilitation support" }
      ],
      misc: [
        { name: "herding" },
        { name: "police K9" },
        { name: "movie dogs" },
        { name: "sled dogs" },
        { name: "military working dogs" },
        { name: "farm dogs" },
        { name: "dock diving" },
        { name: "agility competition" }
      ]
    };
    
    // Generate balanced circle data with equal number in each quadrant
    function generateCircles() {
      const generatedCircles = [];
      const categories = Object.keys(dogJobs);
      const circlesPerCategory = Math.ceil(numCircles / categories.length);
      
      categories.forEach(category => {
        const jobs = dogJobs[category];
        
        for (let i = 0; i < circlesPerCategory; i++) {
          if (generatedCircles.length >= numCircles) break;
          
          // Cycle through jobs if we have more circles than jobs
          const jobIndex = i % jobs.length;
          const job = jobs[jobIndex];
          
          // Random size between 10 and 20
          const size = Math.random() * 10 + 10;
          
          generatedCircles.push({
            id: generatedCircles.length,
            size: size,
            category: category,
            job: job,
            initialX: Math.random() * 100, // Using percentages now
            initialY: Math.random() * 100,
            finalX: 0, // Will be calculated later
            finalY: 0,  // Will be calculated later
            hover: false
          });
        }
      });
      
      return generatedCircles;
    }
    
    // Calculate circle styles based on current scroll progress
    const getCircleStyles = (circle) => {
      const progress = scrollProgress.value;
      
      // Calculate current position based on progress
      const currentX = circle.initialX + (circle.finalX - circle.initialX) * progress;
      const currentY = circle.initialY + (circle.finalY - circle.initialY) * progress;
      
      return {
        width: `${circle.size}px`,
        height: `${circle.size}px`,
        left: `${currentX}%`,
        top: `${currentY}%`,
        backgroundColor: circle.hover ? '#555' : '#000',
        transform: circle.hover ? 'scale(1.3)' : 'scale(1)'
      };
    };
    
    // Set up the quadrant system
    function setupQuadrants() {
      if (!fixedContainer.value) return;
      
      // Map categories to quadrants with explicit coordinates (percentages)
      const quadrants = {
        'sniffers': { x: 25, y: 25 },
        'guides': { x: 75, y: 25 },
        'medical': { x: 25, y: 75 },
        'misc': { x: 75, y: 75 }
      };
      
      // Calculate final positions for circles using percentages
      circles.value.forEach(circle => {
        const quadrant = quadrants[circle.category];
        
        // Add some random variation within each quadrant (percentage based)
        const spreadX = 15;
        const spreadY = 15;
        
        circle.finalX = quadrant.x + (Math.random() - 0.5) * spreadX;
        circle.finalY = quadrant.y + (Math.random() - 0.5) * spreadY;
      });
      
      // Update category label positions
      if (sniffersLabel.value) sniffersLabel.value.style.left = '25%';
      if (sniffersLabel.value) sniffersLabel.value.style.top = '25%';
      if (guidesLabel.value) guidesLabel.value.style.left = '75%';
      if (guidesLabel.value) guidesLabel.value.style.top = '25%';
      if (medicalLabel.value) medicalLabel.value.style.left = '25%';
      if (medicalLabel.value) medicalLabel.value.style.top = '75%';
      if (miscLabel.value) miscLabel.value.style.left = '75%';
      if (miscLabel.value) miscLabel.value.style.top = '75%';
      
      // Set axis positions
      if (horizontalAxis.value) horizontalAxis.value.style.top = '50%';
      if (verticalAxis.value) verticalAxis.value.style.left = '50%';
    }

    function showJobDetails(circle, event) {
      if (!highlightedJob.value) return;
      
      // Update circle hover state
      circle.hover = true;
      
      // Update highlighted job content
      const jobElement = highlightedJob.value;
      const titleElement = jobElement.querySelector('h3');
      const descElement = jobElement.querySelector('p');
      
      if (titleElement) titleElement.textContent = circle.job.name;
      
      if (descElement) {
        if (circle.job.description) {
          descElement.textContent = circle.job.description;
          descElement.style.display = 'block';
        } else {
          descElement.style.display = 'none';
        }
      }
      
      // Position the popup next to the circle
      const rect = event.target.getBoundingClientRect();
      jobElement.style.left = `${rect.right + 10}px`;
      jobElement.style.top = `${rect.top - 20}px`;
      jobElement.style.opacity = '1';
    }

    function hideJobDetails() {
      if (!highlightedJob.value) return;
      
      // Reset hover state for all circles
      circles.value.forEach(c => c.hover = false);
      
      // Hide popup
      highlightedJob.value.style.opacity = '0';
    }

    function setupScrollAnimation() {
      // Create scroll trigger animation
      scrollTrigger.value = ScrollTrigger.create({
        trigger: '.matrix-container',
        start: 'top top',
        end: 'bottom+=1000 top',
        scrub: true,
        pin: true,
        onUpdate: (self) => {
          // Update scroll progress value
          scrollProgress.value = self.progress;
          
          // First phase: Fade out header text (0-0.5 progress)
          const headerPhase = Math.min(scrollProgress.value * 2, 1);
          const headerOpacity = 1 - headerPhase;
          
          const header = document.querySelector('.matrix-container .header');
          const subtext = document.querySelector('.matrix-container .subtext');
          const instruction = document.querySelector('.matrix-container .instruction');
          
          if (header && subtext && instruction) {
            header.style.opacity = headerOpacity;
            subtext.style.opacity = headerOpacity;
            instruction.style.opacity = headerOpacity;
          }
          
          // Second phase: Show matrix and move circles (0.5-1.0 progress)
          const matrixPhase = Math.max(scrollProgress.value * 2 - 1, 0);
          
          if (horizontalAxis.value && verticalAxis.value) {
            horizontalAxis.value.style.opacity = matrixPhase;
            verticalAxis.value.style.opacity = matrixPhase;
          
            document.querySelectorAll('.category-label').forEach(label => {
              label.style.opacity = matrixPhase;
            });
          }
        }
      });
    }

    onMounted(() => {
      console.log('MatrixSection mounted');
      
      // Initialize with circle data
      circles.value = generateCircles();
      console.log(`Generated ${circles.value.length} circles`);
      
      // Setup quadrants once the component is mounted
      setupQuadrants();
      
      // Setup scroll animation
      setupScrollAnimation();
      
      // Handle window resize
      window.addEventListener('resize', setupQuadrants);
    });

    onBeforeUnmount(() => {
      window.removeEventListener('resize', setupQuadrants);
      if (scrollTrigger.value) {
        scrollTrigger.value.kill();
      }
    });

    return {
      circles,
      fixedContainer,
      horizontalAxis,
      verticalAxis,
      highlightedJob,
      sniffersLabel,
      guidesLabel,
      medicalLabel,
      miscLabel,
      getCircleStyles,
      showJobDetails,
      hideJobDetails
    };
  }
};
</script>

<style scoped>

.matrix-container {
  height: 200vh;
  width: 100%;
  position: relative;
  /* background-color: #f5f5f2; */
}

.fixed-container {
  position: sticky;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  overflow: hidden;
  /* background-color: #f5f5f2; */
}

.header {
  position: absolute;
  top: 10%;
  left: 0;
  width: 100%;
  text-align: center;
  font-size: 3rem;
  transition: opacity 0.5s;
  font-family: "ivypresto-headline", serif;
  color: #FF0000;
  font-weight: normal;
}

.subtext {
  position: absolute;
  top: 20%;
  left: 0;
  width: 100%;
  text-align: center;
  color: #888;
  font-size: 1.2rem;
  transition: opacity 0.5s;
}

.instruction {
  position: absolute;
  top: 28%;
  left: 0;
  width: 100%;
  text-align: center;
  color: #aaa;
  font-size: 1rem;
  transition: opacity 0.5s;
}

.category-label {
  position: absolute;
  font-size: 1.8rem;
  opacity: 0;
  transition: opacity 0.5s;
  color: #FF0000;
  font-family: "ivypresto-headline", serif;
  font-weight: normal;
  transform: translate(-50%, -50%);
}

.highlighted-job {
  position: fixed;
  background-color: white;
  border-radius: 5px;
  padding: 15px;
  width: 200px;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.3s;
  z-index: 100;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.highlighted-job h3 {
  margin: 0 0 10px 0;
  font-size: 16px;
  font-family: "ivypresto-headline", serif;
}

.highlighted-job p {
  margin: 0;
  font-size: 12px;
  color: #555;
}

.highlighted-job .learn-more {
  display: block;
  margin-top: 10px;
  color: #888;
  font-size: 0.8rem;
}

.axis {
  position: absolute;
  background-color: #000;
  opacity: 0;
  transition: opacity 0.5s;
}

.horizontal-axis {
  height: 1px;
  width: 80%;
  left: 10%;
  transform: translateY(-50%);
}

.vertical-axis {
  width: 1px;
  height: 80%;
  top: 10%;
  transform: translateX(-50%);
}

.circle {
  position: absolute;
  border-radius: 50%;
  background-color: #000;
  transition: all 0.5s cubic-bezier(0.22, 1, 0.36, 1);
  cursor: pointer;
  transform-origin: center center;
}

.circle:hover {
  transform: scale(1.3);
  background-color: #555;
}
</style>