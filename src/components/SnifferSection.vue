<template>
    <div class="sniffer-section">
      <!-- Scrollama container -->
      <div class="scrollama-container">
        <!-- Step 1: Initial question -->
        <div class="step step-1" data-step="1" ref="step1">
          <h2 class="step-heading" ref="heading1">why so many sniffers?</h2>
        </div>
        
        <!-- Step 2: Human receptors -->
        <div class="step step-2" data-step="2" ref="step2">
          <h2 class="step-heading" ref="heading2">
            <span class="line-wrapper"><span class="line">humans have 5-6 million olfactory</span></span>
            <span class="line-wrapper"><span class="line">receptors (transmit odor info to the brain)</span></span>
          </h2>
          <div class="dots-container human-dots" ref="humanDots"></div>
        </div>
        
        <!-- Step 3: Dog receptors -->
        <div class="step step-3" data-step="3" ref="step3">
          <h2 class="step-heading" ref="heading3">
            <span class="line-wrapper"><span class="line">dogs have</span></span>
            <span class="line-wrapper"><span class="line highlight">5x</span></span>
            <span class="line-wrapper"><span class="line">the olfactory</span></span>
            <span class="line-wrapper"><span class="line">receptors that</span></span>
            <span class="line-wrapper"><span class="line">humans have</span></span>
          </h2>
          <div class="dots-container combined-dots" ref="combinedDots">
            <div class="dots-human" ref="humanDotsSmall"></div>
            <div class="dots-dog" ref="dogDots"></div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import { ref, onMounted, onBeforeUnmount } from 'vue';
  import scrollama from 'scrollama';
  import { gsap } from 'gsap';
  import * as d3 from 'd3';
  
  export default {
    name: 'SnifferSection',
    setup() {
      // Reference elements
      const step1 = ref(null);
      const step2 = ref(null);
      const step3 = ref(null);
      const heading1 = ref(null);
      const heading2 = ref(null);
      const heading3 = ref(null);
      const humanDots = ref(null);
      const humanDotsSmall = ref(null);
      const dogDots = ref(null);
      const combinedDots = ref(null);
      
      // Scrollama instance
      let scroller = null;
      
      // Dot data
      const humanDotCount = 50; // Represents 5-6 million
      const dogDotCount = 250;  // 5x more than humans
      
      // Text animation functions
      const animateQuestionText = () => {
        if (!heading1.value) return;
        
        // Split text into characters
        const text = heading1.value.textContent;
        let html = '';
        
        // Create spans for each character
        for (let i = 0; i < text.length; i++) {
          html += `<span class="char">${text[i] === ' ' ? '&nbsp;' : text[i]}</span>`;
        }
        
        heading1.value.innerHTML = html;
        
        // Staggered character animation
        gsap.fromTo(
          heading1.value.querySelectorAll('.char'),
          { 
            opacity: 0,
            y: 50,
            rotationX: -90
          },
          { 
            opacity: 1, 
            y: 0,
            rotationX: 0,
            stagger: 0.03,
            duration: 0.8,
            ease: "back.out(1.7)",
            onComplete: () => {
              // Pulse animation for question mark
              const questionMark = heading1.value.querySelector('.char:last-child');
              if (questionMark && questionMark.textContent === '?') {
                gsap.to(questionMark, {
                  scale: 1.4,
                  duration: 0.5,
                  repeat: 1,
                  yoyo: true,
                  ease: "power1.inOut"
                });
              }
            }
          }
        );
      };
      
      // Line by line text reveal animation
      const animateLineReveal = (headingRef) => {
        if (!headingRef) return;
        
        gsap.fromTo(
          headingRef.querySelectorAll('.line'),
          { 
            y: "100%",
            opacity: 0 
          },
          { 
            y: "0%",
            opacity: 1,
            stagger: 0.15,
            duration: 0.7,
            ease: "power2.out"
          }
        );
        
        // Special animation for the 5x text if it exists
        const highlightEl = headingRef.querySelector('.highlight');
        if (highlightEl) {
          gsap.fromTo(
            highlightEl,
            { 
              scale: 0.5, 
              opacity: 0 
            },
            { 
              scale: 1,
              opacity: 1,
              delay: 0.5,
              duration: 0.8,
              ease: "elastic.out(1, 0.5)"
            }
          );
        }
      };
      
      // Generate random positions for dots
      const generateDots = (container, count, className, color, delay = 0) => {
        if (!container) return;
        
        const width = container.offsetWidth;
        const height = container.offsetHeight;
        
        // Clear container
        container.innerHTML = '';
        
        // Create dots with D3
        const svg = d3.select(container)
          .append('svg')
          .attr('width', width)
          .attr('height', height);
        
        // Generate random positions
        const data = Array.from({ length: count }, () => ({
          x: Math.random() * width * 0.8 + width * 0.1, // Keep away from edges
          y: Math.random() * height * 0.8 + height * 0.1,
          r: Math.random() * 15 + 10 // Radius between 10-25px
        }));
        
        // Create circles with initial opacity 0
        svg.selectAll('circle')
          .data(data)
          .enter()
          .append('circle')
          .attr('class', className)
          .attr('cx', d => d.x)
          .attr('cy', d => d.y)
          .attr('r', d => d.r)
          .attr('fill', color)
          .attr('opacity', 0)
          .attr('fill-opacity', 0.7);
        
        // Animate dots appearance with GSAP
        gsap.to(`${container.classList.contains('human-dots') ? '.human-dots' : '.dots-human'} circle`, {
          opacity: 1,
          stagger: 0.01,
          delay: delay,
          duration: 0.5
        });
        
        return svg;
      };
      
      // Create combined visualization with force simulation
      const createCombinedVisualization = () => {
        if (!combinedDots.value) return;
        
        const container = combinedDots.value;
        const width = container.offsetWidth;
        const height = container.offsetHeight;
        
        // Clear container
        container.innerHTML = '';
        
        // Create SVG
        const svg = d3.select(container)
          .append('svg')
          .attr('width', width)
          .attr('height', height);
        
        // Generate human dots data
        const humanData = Array.from({ length: humanDotCount }, () => ({
          x: Math.random() * width * 0.6 + width * 0.2,
          y: Math.random() * height * 0.6 + height * 0.2,
          r: Math.random() * 10 + 8,
          type: 'human'
        }));
        
        // Generate dog dots data (initially offscreen)
        const dogData = Array.from({ length: dogDotCount }, () => ({
          x: width + Math.random() * 100,
          y: Math.random() * height,
          r: Math.random() * 10 + 8,
          type: 'dog'
        }));
        
        // Combine data
        const allData = [...humanData, ...dogData];
        
        // Create circles
        const circles = svg.selectAll('circle')
          .data(allData)
          .enter()
          .append('circle')
          .attr('class', d => d.type === 'human' ? 'human-circle' : 'dog-circle')
          .attr('cx', d => d.x)
          .attr('cy', d => d.y)
          .attr('r', d => d.r)
          .attr('fill', d => d.type === 'human' ? '#FFD966' : '#5B9BD5')
          .attr('opacity', d => d.type === 'human' ? 1 : 0)
          .attr('fill-opacity', 0.7);
        
        // Create force simulation
        const simulation = d3.forceSimulation(allData)
          .force('charge', d3.forceManyBody().strength(-5))
          .force('collide', d3.forceCollide().radius(d => d.r + 1).iterations(2))
          .force('x', d3.forceX().x(width/2).strength(0.01))
          .force('y', d3.forceY().y(height/2).strength(0.01))
          .on('tick', () => {
            circles
              .attr('cx', d => d.x = Math.max(d.r, Math.min(width - d.r, d.x)))
              .attr('cy', d => d.y = Math.max(d.r, Math.min(height - d.r, d.y)));
          });
        
        // Store simulation for later use
        container._simulation = simulation;
        
        // Initially pause simulation
        simulation.stop();
        
        return { svg, circles, simulation };
      };
      
      // Animate dog dots entering and pushing human dots
      const animateDogDotsEntering = () => {
        if (!combinedDots.value || !combinedDots.value._simulation) return;
        
        const container = combinedDots.value;
        const simulation = container._simulation;
        
        // Start simulation
        simulation.restart();
        
        // Animate dog circles appearance
        gsap.to('.dog-circle', {
          opacity: 1,
          stagger: 0.005,
          duration: 0.5
        });
        
        // Strengthen the collision force to create a pushing effect
        simulation.force('collide', d3.forceCollide().radius(d => d.r + 2).iterations(4));
        
        // Add a one-time force from the right side
        simulation.force('push', d3.forceX(d => {
          return d.type === 'dog' ? 0.4 * container.offsetWidth : 0.2 * container.offsetWidth;
        }).strength(0.1));
        
        // Gradually reduce the force effect
        setTimeout(() => {
          simulation.force('push', null);
        }, 3000);
      };
      
      // Initialize scrollama
      const initScrollama = () => {
        // Instantiate scrollama
        scroller = scrollama();
        
        // Setup the instance, pass callback functions
        scroller
          .setup({
            step: '.step',
            offset: 0.5,
            debug: false
          })
          .onStepEnter(response => {
            const { element, index, direction } = response;
            
            // Reset all steps
            document.querySelectorAll('.step').forEach(el => {
              el.classList.remove('active');
            });
            
            // Mark the current step as active
            element.classList.add('active');
            
            // Handle different steps with animations
            if (index === 0) {
              // First step: animate the question
              animateQuestionText();
            } else if (index === 1) {
              // Second step: animate text and human dots
              animateLineReveal(heading2.value);
              
              // Delay dots animation to let text animation finish first
              setTimeout(() => {
                if (!humanDots.value._initialized) {
                  generateDots(humanDots.value, humanDotCount, 'human-dot', '#FFD966');
                  humanDots.value._initialized = true;
                }
              }, 800);
            } else if (index === 2) {
              // Third step: animate text and combined visualization
              animateLineReveal(heading3.value);
              
              // Delay combined visualization to let text animation finish first
              setTimeout(() => {
                if (!combinedDots.value._initialized) {
                  createCombinedVisualization();
                  combinedDots.value._initialized = true;
                  
                  // Add a slight delay before dog dots enter
                  setTimeout(() => {
                    animateDogDotsEntering();
                  }, 1000);
                }
              }, 1300);
            }
          })
          .onStepExit(response => {
            // Optional: handle step exit
          });
      };
      
      onMounted(() => {
        // Prepare headings for animations
        if (heading2.value) {
          gsap.set(heading2.value.querySelectorAll('.line'), { 
            y: "100%",
            opacity: 0
          });
        }
        
        if (heading3.value) {
          gsap.set(heading3.value.querySelectorAll('.line'), { 
            y: "100%",
            opacity: 0
          });
          
          if (heading3.value.querySelector('.highlight')) {
            gsap.set(heading3.value.querySelector('.highlight'), {
              scale: 0.5,
              opacity: 0
            });
          }
        }
        
        // Initialize scrollama after components are mounted
        initScrollama();
        
        // Handle window resize
        const handleResize = () => {
          // Update scrollama
          scroller.resize();
          
          // Reset visualizations if window size changes significantly
          if (humanDots.value && humanDots.value._initialized) {
            humanDots.value._initialized = false;
            humanDots.value.innerHTML = '';
          }
          
          if (combinedDots.value && combinedDots.value._initialized) {
            combinedDots.value._initialized = false;
            if (combinedDots.value._simulation) {
              combinedDots.value._simulation.stop();
              combinedDots.value._simulation = null;
            }
            combinedDots.value.innerHTML = '';
          }
        };
        
        window.addEventListener('resize', handleResize);
        
        // Cleanup on unmount
        onBeforeUnmount(() => {
          window.removeEventListener('resize', handleResize);
          if (scroller) scroller.destroy();
          
          // Stop any running simulations
          if (combinedDots.value && combinedDots.value._simulation) {
            combinedDots.value._simulation.stop();
          }
        });
      });
      
      return {
        step1,
        step2,
        step3,
        heading1,
        heading2,
        heading3,
        humanDots,
        humanDotsSmall,
        dogDots,
        combinedDots
      };
    }
  };
  </script>
  
  <style scoped>
  .sniffer-section {
    position: relative;
    width: 100%;
    min-height: 300vh; /* Three full viewport heights */
    background-color: #F2EFEB;
  }
  
  .scrollama-container {
    position: relative;
    width: 100%;
    padding: 0;
  }
  
  .step {
    position: relative;
    height: 100vh;
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    opacity: 0.2;
    transition: opacity 0.5s;
    overflow: hidden;
  }
  
  .step.active {
    opacity: 1;
  }
  
  .step-heading {
    font-family: "ivypresto-headline", serif;
    font-size: 42px;
    text-align: center;
    max-width: 800px;
    padding: 0 20px;
    line-height: 1.3;
    margin-bottom: 50px;
    z-index: 10;
  }
  
  /* New styles for text animation */
  .char {
    display: inline-block;
    transform-origin: center bottom;
  }
  
  .line-wrapper {
    display: block;
    overflow: hidden;
    line-height: 1.3;
  }
  
  .line {
    display: block;
    transform-origin: center bottom;
  }
  
  .dots-container {
    position: relative;
    width: 80%;
    max-width: 1000px;
    height: 60vh;
    margin: 0 auto;
  }
  
  .highlight {
    color: #5B9BD5;
    font-size: 100px;
    line-height: 1;
    display: inline-block;
    transform-origin: center;
  }
  
  /* Step-specific styles */
  .step-1 {
    padding-top: 10vh;
  }
  
  .step-3 .step-heading {
    position: absolute;
    top: 50%;
    left: 10%;
    transform: translateY(-50%);
    text-align: left;
    width: 30%;
    max-width: 400px;
    margin: 0;
  }
  
  .step-3 .dots-container {
    position: absolute;
    top: 50%;
    right: 5%;
    transform: translateY(-50%);
    width: 60%;
    height: 80vh;
  }
  
  /* Responsive adjustments */
  @media (max-width: 768px) {
    .step-heading {
      font-size: 32px;
    }
    
    .step-3 .step-heading {
      position: relative;
      top: auto;
      left: auto;
      transform: none;
      text-align: center;
      width: 100%;
      margin-bottom: 30px;
    }
    
    .step-3 .dots-container {
      position: relative;
      top: auto;
      right: auto;
      transform: none;
      width: 90%;
      height: 50vh;
    }
    
    .highlight {
      font-size: 60px;
    }
  }
  </style>