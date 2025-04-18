<template>
  <div class="gsap-section">
    <nav ref="navRef"><a href="#">Notable Good Boys &amp; Girls</a></nav>


    <div class="container">

      <section class="wrapper-conclusion" ref="wrapperConclusion">
   


        <div class="card" id="card-1" ref="card1">
          <div class="card-image">
            <img src="@/assets/images/eba.jpg" alt="Eba" />
          </div>
          <div class="card-content">
            <h2 class="card-title">Eba</h2>
            <h3 class="card-subtitle">The Whale Sniffer</h3>
            <p class="card-body">Eba is a sniffer dog that helps researchers locate whale poop. She can smell whale scat up to a nautical mile away. Her work helps researchers analyze the reproductive and nutrition hormones, and toxicants, which can tell a larger story about ocean conservation.</p>
          </div>
        </div>
        
        <div class="card" id="card-2" ref="card2">
          <div class="card-image">
            <img src="@/assets/images/elvis.jpg" alt="Elvis" />
          </div>
          <div class="card-content">
            <h2 class="card-title">Elvis</h2>
            <h3 class="card-subtitle">Polar Bear Pregnancy Detector</h3>
            <p class="card-body">At just 2 years old, Elvis was trained to detect polar bear pregnancies by scent. Inspired by cancer-sniffing dogs, a Cincinnati Zoo scientist developed the idea to help confirm pregnancies early—crucial for preparing the bears and their environment for cubs.</p>
          </div>
        </div>
        
        <div class="card" id="card-3" ref="card3">
          <div class="card-image">
            <img src="@/assets/images/peppi.jpg" alt="Peppi" />
          </div>
          <div class="card-content">
            <h2 class="card-title">Hospital Companion</h2>
            <h3 class="card-subtitle">Emotional Support</h3>
            <p class="card-body">Peppi works at HCA HealthONE Rose medical center in Denver where she helps ease anxiety and burnout for her colleagues who have high stress jobs in the ER.</p>
          </div>
        </div>
        
        <div class="card" id="card-4" ref="card4">
          <div class="card-image">
            <img src="@/assets/images/img-4.jpg" alt="Card image 4" />
          </div>
          <div class="card-content">
            <h2 class="card-title">Medical Detection</h2>
            <h3 class="card-subtitle">Disease Sniffer</h3>
            <p class="card-body">These specialized dogs can detect certain diseases through scent, including cancer, diabetes, and even COVID-19. Their incredible sense of smell allows them to identify specific chemical compounds associated with various medical conditions, often before traditional tests can detect them.</p>
          </div>
        </div>
        <h1>Sniffers therapists medical random!</h1>
      </section>

      <section class="outro">
        <h1>Conclusion Paragraph</h1>
      </section>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

export default {
  name: 'OutroSection',
  setup() {
    const wrapperConclusion = ref(null);
    const card1 = ref(null);
    const card2 = ref(null);
    const card3 = ref(null);
    const card4 = ref(null);
    const navRef = ref(null); // Add ref for nav element

    // Store ScrollTrigger instances for cleanup
    const scrollTriggers = [];

    onMounted(() => {
      // Ensure elements are available
      if (!wrapperConclusion.value) return;

      // Make nav fixed when the section is in view
      const navTrigger = ScrollTrigger.create({
        trigger: ".gsap-section",
        start: "top 10%", // When top of section is 10% from viewport top
        end: "bottom 10%", // When bottom of section is 10% from viewport bottom
        onEnter: () => {
          gsap.to(".gsap-section nav", {
            position: "fixed",
            top: "20px",
            opacity: 1,
            duration: 0.3
          });
        },
        onLeave: () => {
          gsap.to(".gsap-section nav", {
            opacity: 0,
            duration: 0.3
          });
        },
        onEnterBack: () => {
          gsap.to(".gsap-section nav", {
            position: "fixed",
            top: "20px",
            opacity: 1,
            duration: 0.3
          });
        },
        onLeaveBack: () => {
          gsap.to(".gsap-section nav", {
            opacity: 0,
            duration: 0.3
          });
        }
      });
      scrollTriggers.push(navTrigger);

      // Your existing code continues...
      const cards = [
        { ref: card1, id: "#card-1", endTranslateX: -500, rotate: 10 },
        { ref: card2, id: "#card-2", endTranslateX: -1000, rotate: -30 },
        { ref: card3, id: "#card-3", endTranslateX: -2000, rotate: 45 },
        { ref: card4, id: "#card-4", endTranslateX: -1500, rotate: -30 },
      ];

      // Main wrapper animation
      const mainTrigger = ScrollTrigger.create({
        trigger: wrapperConclusion.value,
        start: "top top",
        end: "+=900vh", // How long the pinning lasts
        scrub: 1,
        pin: true,
        anticipatePin: 1,
        onUpdate: (self) => {
          gsap.to(wrapperConclusion.value, {
            x: `${-600 * self.progress}vw`,
            duration: 0.5,
            ease: "power3.out",
          });
        },
      });
      scrollTriggers.push(mainTrigger);

      // Individual card animations
      cards.forEach((card) => {
        if (!card.ref.value) return;

        // Create a GSAP timeline for each card to ensure synchronized animations
        const tl = gsap.timeline({
          scrollTrigger: {
            trigger: card.ref.value,
            start: "top top",
            end: "+=1200vh",
            scrub: 1,
            onUpdate: (self) => {
              // Apply animation to the card element itself
              gsap.to(card.ref.value, {
                x: `${card.endTranslateX * self.progress}px`,
                rotate: `${card.rotate * self.progress * 2}deg`,
                duration: 0.5,
                ease: "power3.out",
                // Force 3D transforms to ensure proper stacking and prevent layout issues
                force3D: true,
                // Ensure transforms are applied to the entire card as a unit
                transformOrigin: "center center"
              });
            }
          }
        });
        
        // Make sure internal elements maintain their positions relative to the card
        const cardContent = card.ref.value.querySelector('.card-content');
        const cardImage = card.ref.value.querySelector('.card-image');
        
        if (cardContent && cardImage) {
          // Set initial positions for internal elements to prevent movement during animation
          gsap.set([cardContent, cardImage], {
            position: "relative",
            xPercent: 0,
            yPercent: 0
          });
        }
        
        scrollTriggers.push(tl.scrollTrigger);
      });

      // Outro text animation
      const outroTrigger = ScrollTrigger.create({
        trigger: ".gsap-section .outro",
        start: "top 80%",
        toggleActions: "play none none reverse",
        onEnter: () => {
          gsap.fromTo(".gsap-section .outro h1",
            { opacity: 0, y: 30 },
            { opacity: 1, y: 0, duration: 1, ease: "power2.out" }
          );
        }
      });
      scrollTriggers.push(outroTrigger);
    });

    onBeforeUnmount(() => {
      // Kill all ScrollTrigger instances associated with this component
      scrollTriggers.forEach(trigger => trigger.kill());
      console.log("OutroSection ScrollTriggers killed");
    });

    return {
      wrapperConclusion,
      card1,
      card2,
      card3,
      card4,
      navRef // Add navRef to returned refs
    };
  },
};
</script>

<style scoped>
/* Apply the provided CSS within the scoped style block */
.gsap-section {
  position: relative;
  margin-top: 100px; /* Add space after the previous section */
  background-color: #F2EFEB; /* Match background */
  overflow: hidden; /* Prevent horizontal scrollbar */
}

.gsap-section img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.gsap-section a {
  text-decoration: none;
  color: black;
  font-size: 20px;
}

.gsap-section .container {
  width: 100%;
  position: relative;
}

.gsap-section .wrapper-conclusion {
  position: relative; /* Changed from absolute for pinning */
  top: 0;
  width: 700vw; /* Very wide to allow horizontal movement */
  height: 100vh; /* Full viewport height for pinning */
  will-change: transform;
  display: flex; /* Helps align items if needed, though cards are absolute */
  align-items: center; /* Vertically center content if needed */
}

.gsap-section .wrapper-conclusion h1 {
  position: absolute;
  left: 40vw; /* Increased from 5vw to 15vw to add more space */
  top: 43%;
  transform: translateY(-50%);
  width: auto;
  color: red;
  font-size: 35vw;
  font-weight: 400;
  font-family: "ivypresto-headline", serif;
  font-style: italic;
  text-align: left;
  margin: 0;
  z-index: 1;
  white-space: nowrap;
}

.gsap-section .card {
  position: absolute;
  width: 800px; /* Increased to accommodate image + text */
  height: 300px;
  background: transparent; /* Changed to transparent */
  border-radius: 20px;
  overflow: visible; /* Changed to visible for text overflow */
  display: flex; /* Use flexbox to arrange image and text side by side */
  align-items: center;
  z-index: 5; /* Add this line to ensure cards are above the heading */
}

.gsap-section .card-image {
  width: 300px;
  height: 300px;
  border-radius: 20px;
  overflow: hidden;
  box-shadow: 0 10px 20px rgba(0,0,0,0.2);
  flex-shrink: 0; /* Prevent image from shrinking */
}

.gsap-section .card-content {
  padding-left: 30px;
  text-align: left;
  max-width: 450px;
  position: relative;
  overflow: visible;
  /* These properties help maintain the internal structure during transforms */
  transform-style: preserve-3d;
  backface-visibility: hidden;
}

.gsap-section .card-title {
  font-family: "ivypresto-headline", serif;
  font-size: 32px;
  margin: 0 0 5px 0;
  font-weight: 600;
}

.gsap-section .card-subtitle {
  font-family: "ivypresto-headline", serif;
  font-size: 18px;
  margin: 0 0 15px 0;
  font-weight: 400;
  font-style: italic;
}

.gsap-section .card-body {
  font-family: neue-haas-grotesk-display, sans-serif;
  font-weight: 300;
  font-style: normal;
  color: black;
  font-size: 14px;
  line-height: normal;
  margin: 0;
  text-align: left;
  position: relative;
  max-width: 250px;  overflow-wrap: break-word;
  word-wrap: break-word;
  margin-top: 70px;
}

/* Position cards relative to the wide wrapper-conclusion */
.gsap-section #card-1 {
  top: 80%;
  left: 47vw; /* Adjust positioning within the wide wrapper */
  transform: translateY(-50%);
}

.gsap-section #card-2 {
  top: 20%;
  left: 110vw;
  transform: translateY(-50%);
}

.gsap-section #card-3 {
  top: 80%;
  left: 170vw;
  transform: translateY(-50%);
}

.gsap-section #card-4 {
  top: 15%;
  left: 360vw;
  transform: translateY(-50%);
}

.gsap-section .outro {
  position: relative; /* Changed from absolute */
  width: 100%;
  height: 100vh; /* Full height section after the pinned element */
  display: flex; /* Use flexbox for centering */
  justify-content: center;
  align-items: center;
  background-color: #F2EFEB; /* Ensure background matches */
}

.gsap-section .outro h1 {
  width: max-content;
  max-width: 80%; /* Prevent text from being too wide */
  font-size: 40px;
  font-weight: lighter;
  font-family: "ivypresto-headline", serif; /* Added font */
  text-align: center;
  color: #ff0000; /* Changed color to red */
}

@media (max-width: 900px) {
  .gsap-section .wrapper-conclusion h1 {
    font-size: 12vw;
    left: 3vw;
  }
  
  .gsap-section .card {
    width: 500px;
    flex-direction: column; /* Stack image and text vertically on mobile */
    height: auto;
  }
  
  .gsap-section .card-image {
    width: 200px;
    height: 200px;
  }
  
  .gsap-section .card-content {
    padding: 20px 0 0 0;
    max-width: 100%;
  }
  
  .gsap-section .outro h1 {
    font-size: 30px;
  }
  
  /* Mobile-specific nav styles */
  .gsap-section nav a {
    font-size: 18px;
    padding: 8px 15px;
  }
}

/* Update nav styles to work with the ScrollTrigger animation */
.gsap-section nav {
  position: fixed; /* Change back to fixed */
  top: 20px;
  left: 0;
  width: 100%;
  padding: 1em;
  display: flex;
  justify-content: center;
  z-index: 100;
  opacity: 0; /* Start hidden, will be shown by ScrollTrigger */
}

.gsap-section nav a {
  text-decoration: none;
  color: black;
  font-size: 24px;
  font-family: "ivypresto-headline", serif;
  font-style: italic;
  background-color: rgba(242, 239, 235, 0.9); /* More opaque for better visibility */
  padding: 10px 20px;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
}

.gsap-section nav a:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.15);
}
</style>