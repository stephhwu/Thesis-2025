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
    
    <!-- <div class="vertical-line"></div> -->
    
    <div class="marker-wrapper">
      <div class="marker">
        <div class="grab"></div>
      </div>
      <div class="timeline-year">1700s</div>
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
import { onMounted, ref, onBeforeUnmount } from 'vue';
import { gsap } from 'gsap';

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
    let target = 0;
    let current = 0;
    let ease = 0.075;
    let maxScroll = 0;
    let animationFrame = null;
    let wheelListener = null;
    let resizeListener = null;
    
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
    
    // Track if we're at the beginning of the timeline
    const isAtStart = () => {
      return target <= 0;
    };

    // Add flag to track if we're at the end of the timeline
    const isAtEnd = () => {
      return target >= maxScroll - 10; // Allow small buffer for rounding errors
    };

    function lerp(start, end, factor) {
      return start + (end - start) * factor;
    }

    function updateTimelineYear(markerMove, markerMaxMove) {
      const partWidth = markerMaxMove / timelineItems.length;
      let currentIndex = Math.min(
        timelineItems.length - 1, 
        Math.max(0, Math.round((markerMove - 70) / partWidth))
      );
      document.querySelector(".timeline-year").textContent = timelineItems[currentIndex].year;
    }

    function update() {
      current = lerp(current, target, ease);

      gsap.set(".slider-wrapper", {
        x: -current,
      });

      let moveRatio = current / maxScroll;
      let markerMaxMove = window.innerWidth - document.querySelector(".marker-wrapper").offsetWidth - 170;
      let markerMove = 70 + moveRatio * markerMaxMove;
      
      gsap.set(".marker-wrapper", {
        x: markerMove,
      });

      updateTimelineYear(markerMove, markerMaxMove);

      animationFrame = requestAnimationFrame(update);
    }

    onMounted(() => {
      maxScroll = sliderWrapper.value.offsetWidth - window.innerWidth;
      
      // Update wheel event listener to allow vertical scrolling at end of timeline
      wheelListener = (e) => {
        // If scrolling up and at start, go back to settings
        if (e.deltaY < 0 && isAtStart()) {
          emit('scroll-top');
          return;
        }
        
        // If scrolling down and at end, allow normal page scrolling
        if (e.deltaY > 0 && isAtEnd()) {
          return; // Don't prevent default - allow scrolling to next section
        }
        
        // Otherwise use scroll for horizontal movement
        target += e.deltaY;
        target = Math.max(0, Math.min(maxScroll, target));
        
        // Prevent default to avoid page scrolling during horizontal navigation
        e.preventDefault();
      };
      
      window.addEventListener("wheel", wheelListener, { passive: false });
      
      // Add resize listener
      resizeListener = () => {
        maxScroll = sliderWrapper.value.offsetWidth - window.innerWidth;
      };
      
      window.addEventListener("resize", resizeListener);
      
      // Start animation
      update();
      
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
    });
    
    onBeforeUnmount(() => {
      window.removeEventListener("wheel", wheelListener, { passive: false });
      window.removeEventListener("resize", resizeListener);
      cancelAnimationFrame(animationFrame);
    });

    return {
      sliderWrapper,
      timelineItems
    };
  }
};
</script>

<style scoped>

.timeline-container {
  width: 100%;
  height: 100vh;
  /* background: #f2efea; */
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


/* Individual character styling for animation */
.timeline-title .char {
  display: inline-block;
  white-space: pre;
}

.slider {
  width: 100%;
  height: 70vh;
  overflow: hidden;
  margin-top: 150px; /* Push content down to make room for the title */
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
  /* background: #f2efea; */
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
}

.timeline-year {
  position: absolute;
  top: 125px; /* Positioned near the circle */
  left: 40px;
  font-family: "ivypresto-headline", serif;
  font-size: 18px;
  color: #000;
}
</style>