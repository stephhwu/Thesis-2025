<template>
  <div class="tech-vs-dog-section">
    <div class="section-content">
      <h1 class="main-title">Technology vs Dog</h1>
      
      <div class="comparison-block">
        <h2 class="comparison-title">Specificity of Lung Cancer Detection</h2>
        
        <div class="progress-row">
          <div class="label">Working Dog</div>
          <div class="progress-container">
            <div class="progress-bar dog"></div>
          </div>
          <div class="percentage">93%-99%*</div>
        </div>
        
        <div class="progress-row">
          <div class="label">Technology</div>
          <div class="progress-container">
            <div class="progress-bar tech"></div>
          </div>
          <div class="percentage">92.6%</div>
        </div>
      </div>
      
      <div class="comparison-block cost-block">
        <h2 class="comparison-title">Cost Comparison<sup>†</sup></h2>
        
        <div class="cost-comparison">
          <div class="comparison-column">
            <div class="image-placeholder">
              <div class="placeholder-text">illustration of dog</div>
            </div>
            
            <div class="entity-label">Working Dog</div>
            
            <div class="price">~$79 per day</div>
            
            <div class="description">Medical Scent Detection</div>
          </div>
          
          <div class="vs">vs</div>
          
          <div class="comparison-column">
            <div class="image-placeholder">
              <div class="placeholder-text">illustration of tech</div>
            </div>
            
            <div class="entity-label">Low-dose CT</div>
            
            <div class="price">$3,074 per person</div>
            
            <div class="description">Equipment &amp; Operation</div>
          </div>
        </div>
        
        <!-- <div class="footnote">
          <p>† This price estimate comes from prior use of medical scent canines used in research for detecting coronavirus disease SARS-COVID-19</p>
        </div> -->
      </div>
    </div>
  </div>
</template>

<script>
import { onMounted } from 'vue';
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

// Register ScrollTrigger plugin
gsap.registerPlugin(ScrollTrigger);

export default {
  name: 'TechVsDogSection',
  setup() {
    onMounted(() => {
      // Animate progress bars when section comes into view
      gsap.fromTo('.progress-bar.dog', 
        { width: '0%' },
        { 
          width: '99%', 
          duration: 1.5, 
          ease: 'power2.out',
          scrollTrigger: {
            trigger: '.progress-row',
            start: 'top 70%',
            toggleActions: 'play none none none'
          }
        }
      );
      
      gsap.fromTo('.progress-bar.tech', 
        { width: '0%' },
        { 
          width: '92.6%', 
          duration: 1.5, 
          ease: 'power2.out',
          scrollTrigger: {
            trigger: '.progress-row',
            start: 'top 70%',
            toggleActions: 'play none none none'
          }
        }
      );
      
      // Animate cost comparison
      gsap.fromTo('.comparison-column', 
        { opacity: 0, y: 20 },
        { 
          opacity: 1, 
          y: 0,
          stagger: 0.3,
          duration: 0.8, 
          ease: 'back.out(1.2)',
          scrollTrigger: {
            trigger: '.cost-comparison',
            start: 'top 70%',
            toggleActions: 'play none none none'
          }
        }
      );
      
      // Animate VS
      gsap.fromTo('.vs', 
        { opacity: 0, scale: 0.6 },
        { 
          opacity: 1, 
          scale: 1,
          duration: 0.8, 
          delay: 0.6,
          ease: 'elastic.out(1.2)',
          scrollTrigger: {
            trigger: '.cost-comparison',
            start: 'top 70%',
            toggleActions: 'play none none none'
          }
        }
      );
    });

    return {};
  }
};
</script>

<style scoped>
.tech-vs-dog-section {
  width: 100%;
  min-height: 100vh;
  background-color: #F2EFEB;
  padding: 80px 0;
  display: flex;
  justify-content: center;
  align-items: center;
  scroll-snap-align: start;
  scroll-snap-stop: always;
}

.section-content {
  width: 90%;
  max-width: 1200px;
  margin: 0 auto;
}

.main-title {
  font-family: "ivypresto-headline", serif;
  font-weight: 400;
  font-style: normal;
  font-size: 55px;
  color: red;
  text-align: center;
  margin-bottom: 60px;
}

.comparison-block {
  margin-bottom: 80px;
}

.comparison-title {
  font-family: "ivypresto-headline", serif;
  font-size: 28px;
  font-weight: 300;
  margin-bottom: 40px;
}

.progress-row {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.label {
  width: 180px;
  font-family: "ivypresto-headline", serif;
  color: #AAA;
  font-size: 24px;
  text-align: right;
  padding-right: 20px;
}

.progress-container {
  flex-grow: 1;
  height: 20px;
  background-color: #E5E5E5;
  border-radius: 10px;
  overflow: hidden;
}

.progress-bar {
  height: 100%;
  border-radius: 10px;
  background-color: #000;
}

.percentage {
  width: 120px;
  font-family: "ivypresto-headline", serif;
  font-size: 20px;
  font-weight: bold;
  text-align: right;
  padding-left: 20px;
}

/* Cost comparison styles */
.cost-block {
  margin-top: 100px;
}

.cost-comparison {
  display: flex;
  justify-content: space-around;
  align-items: center;
  margin-bottom: 40px;
}

.comparison-column {
  width: 40%;
  text-align: center;
}

.image-placeholder {
  width: 100%;
  height: 150px;
  background-color: #E5E5E5;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 20px;
}

.placeholder-text {
  color: #777;
  font-style: italic;
}

.entity-label {
  font-family: "ivypresto-headline", serif;
  color: #AAA;
  font-size: 28px;
  margin-bottom: 10px;
}

.price {
  font-family: "ivypresto-headline", serif;
  font-size: 36px;
  font-weight: bold;
  margin-bottom: 10px;
}

.description {
  font-family: "ivypresto-headline", serif;
  font-size: 18px;
}

.vs {
  font-family: "ivypresto-headline", serif;
  font-size: 36px;
  font-style: italic;
  color: #000;
}

.footnote {
  font-family: "ivypresto-headline", serif;
  font-style: italic;
  font-size: 12px;
  color: #999;
  text-align: center;
  margin-top: 40px;
  max-width: 800px;
  margin-left: auto;
  margin-right: auto;
}

@media (max-width: 768px) {
  .main-title {
    font-size: 40px;
  }
  
  .comparison-title {
    font-size: 24px;
  }
  
  .label {
    width: 120px;
    font-size: 18px;
  }
  
  .percentage {
    width: 90px;
    font-size: 16px;
  }
  
  .cost-comparison {
    flex-direction: column;
  }
  
  .comparison-column {
    width: 80%;
    margin-bottom: 30px;
  }
  
  .vs {
    margin: 20px 0;
  }
  
  .entity-label {
    font-size: 24px;
  }
  
  .price {
    font-size: 28px;
  }
}
</style>