<template>
  <div class="gsap-section">
    <div class="container">
      <section class="wrapper-conclusion" ref="wrapperConclusion">
        <h1>Sniffers Therapists Medical Random!</h1>

        <div class="card-container" id="card-container-1">
          <div class="card" id="card-1" ref="card1">
            <img src="@/assets/images/eba.jpg" alt="Card image 1" />
          </div>
          <div class="card-text">
            <h2 class="card-headline">Eba</h2>
            <p class="card-subtext">The Whale Sniffer</p>
            <p class="card-body">
              Eba is a springer dog that helps researchers locate whale poop. She can smell whale scat up to a nautical mile away. Her work helps researchers analyze the health of whales, especially prey availability, stress hormones, and toxicants, which tell a larger story about ocean conservation.
            </p>
          </div>
        </div>
        
        <div class="card-container" id="card-container-2">
          <div class="card" id="card-2" ref="card2">
            <img src="@/assets/images/img-2.jpg" alt="Card image 2" />
          </div>
          <div class="card-text">
            <h2 class="card-headline">Tracker</h2>
            <p class="card-subtext">The Forest Protector</p>
            <p class="card-body">
              Tracker is a Belgian Malinois trained to detect poachers in wildlife reserves. His keen sense of smell can identify human scents from over a mile away, helping rangers intercept illegal hunters before they reach endangered species. His work has contributed to a 30% reduction in poaching incidents.
            </p>
          </div>
        </div>
        
        <div class="card-container" id="card-container-3">
          <div class="card" id="card-3" ref="card3">
            <img src="@/assets/images/img-3.jpg" alt="Card image 3" />
          </div>
          <div class="card-text">
            <h2 class="card-headline">Luna</h2>
            <p class="card-subtext">The Disease Detective</p>
            <p class="card-body">
              Luna is a Labrador Retriever who detects agricultural diseases before they become visible to farmers. She can identify bacterial and fungal infections in crops weeks before symptoms appear, allowing for targeted treatment and preventing widespread crop loss. Her accuracy rate exceeds 95%.
            </p>
          </div>
        </div>
        
        <div class="card-container" id="card-container-4">
          <div class="card" id="card-4" ref="card4">
            <img src="@/assets/images/img-4.jpg" alt="Card image 4" />
          </div>
          <div class="card-text">
            <h2 class="card-headline">Scout</h2>
            <p class="card-subtext">The Disaster Rescuer</p>
            <p class="card-body">
              Scout is a Border Collie trained for search and rescue after natural disasters. He can detect human scent under debris and rubble, even days after an incident. During his career, he has located over 50 survivors following earthquakes and building collapses around the world.
            </p>
          </div>
        </div>
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

// GSAP plugins are registered globally in DogsWithJobs.vue,
// but we still need to import them here to use them.

export default {
  name: 'OutroSection',
  setup() {
    const wrapperConclusion = ref(null);
    const card1 = ref(null);
    const card2 = ref(null);
    const card3 = ref(null);
    const card4 = ref(null);

    // Store ScrollTrigger instances for cleanup
    const scrollTriggers = [];

    onMounted(() => {
      // Ensure elements are available
      if (!wrapperConclusion.value) return;

      const cards = [
        { ref: card1, id: "#card-1", endTranslateX: -2000, rotate: 55 },
        { ref: card2, id: "#card-2", endTranslateX: -1000, rotate: -30 },
        { ref: card3, id: "#card-3", endTranslateX: -2000, rotate: 45 },
        { ref: card4, id: "#card-4", endTranslateX: -1500, rotate: -30 },
      ];

      // Main wrapper animation
      const mainTrigger = ScrollTrigger.create({
        trigger: wrapperConclusion.value, // Use the ref
        start: "top top",
        end: "+=900vh", // How long the pinning lasts
        scrub: 1,
        pin: true,
        anticipatePin: 1,
        onUpdate: (self) => {
          gsap.to(wrapperConclusion.value, { // Use the ref
            x: `${-600 * self.progress}vw`,
            duration: 0.5, // Keep duration low with scrub for responsiveness
            ease: "power3.out",
          });
        },
      });
      scrollTriggers.push(mainTrigger);

      // Individual card animations
      cards.forEach((card) => {
        if (!card.ref.value) return; // Check if the card element exists

        const cardTrigger = ScrollTrigger.create({
          trigger: card.ref.value, // Use the ref for the trigger
          start: "top top", // Start animation when the card's top hits the viewport top
          end: "+=1200vh", // Duration of the card's animation scroll
          scrub: 1,
          onUpdate: (self) => {
            gsap.to(card.ref.value, { // Use the ref to target the card
              x: `${card.endTranslateX * self.progress}px`,
              rotate: `${card.rotate * self.progress * 2}deg`,
              duration: 0.5,
              ease: "power3.out",
            });
          },
        });
        scrollTriggers.push(cardTrigger);
      });

       // Optional: Animate the conclusion text fade-in
       const outroTrigger = ScrollTrigger.create({
         trigger: ".gsap-section .outro",
         start: "top 80%", // Start when 80% of the outro section is visible
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
    };
  },
};
</script>

<style scoped>
/* Apply the provided CSS within the scoped style block */
.gsap-section {
  position: relative;
  margin-top: 100px; /* Add space after the previous section */
  /* height: 1200px; */ /* Let content define height, pinning handles duration */
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
  /* height: 1200px; */ /* Let content define height */
  position: relative;
}

/* Removed nav styles as it's likely handled globally */

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
  position: absolute; /* Keep h1 positioned relative to the wrapper */
  left: 5vw; /* Position it within the initial view */
  top: 40%;
  transform: translateY(-50%);
  width: auto; /* Let text size determine width */
  color: black;
  font-size: 40vw; /* Adjusted for better fit */
  font-weight: 400;
  font-family: "ivypresto-headline", serif; /* Added font */
  font-style: italic;
  text-align: left;
  margin-left: 200px;
  margin: 0;
  z-index: 10; /* Ensure text is above cards */
  white-space: nowrap; /* Prevent wrapping */
}

/* Card container styles - holds both image and text */
.gsap-section .card-container {
  position: absolute;
  display: flex;
  align-items: center;
  width: 800px; /* Adjust based on your layout needs */
}

/* Position each card container */
.gsap-section #card-container-1 {
  top: 70%;
  left: 20vw; /* Match your existing card positions */
  transform: translateY(-40%);
}

.gsap-section #card-container-2 {
  top: 25%;
  left: 200vw;
  transform: translateY(-50%);
}

.gsap-section #card-container-3 {
  top: 45%;
  left: 280vw;
  transform: translateY(-50%);
}

.gsap-section #card-container-4 {
  top: 15%;
  left: 360vw;
  transform: translateY(-50%);
}

/* Card styles - adjusted to fit within container */
.gsap-section .card {
  position: relative; /* Changed from absolute */
  width: 300px;
  height: 300px;
  margin-right: 40px; /* Space between image and text */
  background: gray;
  border-radius: 20px;
  overflow: hidden;
  box-shadow: 0 10px 20px rgba(0,0,0,0.2);
  flex-shrink: 0; /* Prevent card from shrinking */
}

/* Text styles */
.gsap-section .card-text {
  color: #222;
  max-width: 400px;
}

.gsap-section .card-headline {
  font-family: "ivypresto-headline", serif;
  font-style: italic;
  font-size: 40px;
  margin: 0 0 5px 0;
  color: #ff0000; /* Red color matching your design */
}

.gsap-section .card-subtext {
  font-family: "ivypresto-headline", serif;
  font-style: italic;
  font-size: 20px;
  margin: 0 0 20px 0;
  color: #222;
}

.gsap-section .card-body {
  font-family: "Neue Haas Grotesk Display Pro", Arial, sans-serif;
  font-weight: 200; /* 35 extra light */
  font-size: 14px;
  line-height: 1.5;
  color: #333;
  margin: 0;
  max-width: 380px;
}

/* Mobile adjustments */
@media (max-width: 900px) {
  .gsap-section .card-container {
    flex-direction: column;
    width: auto;
  }
  
  .gsap-section .card {
    margin-right: 0;
    margin-bottom: 20px;
    width: 250px;
    height: 250px;
  }
  
  .gsap-section .card-headline {
    font-size: 30px;
  }
  
  .gsap-section .card-subtext {
    font-size: 16px;
  }
  
  .gsap-section .card-body {
    font-size: 12px;
  }
}

.gsap-section .outro {
  position: relative; /* Changed from absolute */
  /* top: 150vh; */ /* Removed absolute positioning */
  width: 100%;
  height: 100vh; /* Full height section after the pinned element */
  display: flex; /* Use flexbox for centering */
  justify-content: center;
  align-items: center;
  background-color: #F2EFEB; /* Ensure background matches */
}

.gsap-section .outro h1 {
  /* position: absolute; */ /* Removed absolute positioning */
  /* top: 50%; */
  /* left: 50%; */
  /* transform: translate(-50%, -50%); */ /* Centering handled by flexbox */
  width: max-content;
  max-width: 80%; /* Prevent text from being too wide */
  font-size: 40px;
  font-weight: lighter;
  font-family: "ivypresto-headline", serif; /* Added font */
  text-align: center;
  color: #ff0000; /* Changed color to red */
}

@media (max-width: 900px) {
  /* .gsap-section .wrapper-conclusion { */
    /* padding-top: 20em; */ /* Pinning makes padding-top less effective */
  /* } */
  .gsap-section .wrapper-conclusion h1 {
    font-size: 12vw;
    left: 3vw;
  }
  .gsap-section .card-container {
    width: 200px;
  }
  .gsap-section .card {
    width: 200px;
    height: 200px;
  }
   .gsap-section .outro h1 {
     font-size: 30px;
   }
}
</style>