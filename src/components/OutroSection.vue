<template>
  <div class="gsap-section">
    <div class="container">
      <section class="wrapper-conclusion" ref="wrapperConclusion">
        <h1>Sniffers Therapists Medical Random!</h1>

        <div class="card" id="card-1" ref="card1">
          <img src="@/assets/images/img-1.jpg" alt="Card image 1" />
        </div>
        <div class="card" id="card-2" ref="card2">
          <img src="@/assets/images/img-2.jpg" alt="Card image 2" />
        </div>
        <div class="card" id="card-3" ref="card3">
          <img src="@/assets/images/img-3.jpg" alt="Card image 3" />
        </div>
        <div class="card" id="card-4" ref="card4">
          <img src="@/assets/images/img-4.jpg" alt="Card image 4" />
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
        { ref: card1, id: "#card-1", endTranslateX: -2000, rotate: 45 },
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
  top: 50%;
  transform: translateY(-50%);
  width: auto; /* Let text size determine width */
  color: black;
  font-size: 35vw; /* Adjusted for better fit */
  font-weight: 400;
  font-family: "ivypresto-headline", serif; /* Added font */
  text-align: left;
  margin: 0;
  z-index: 10; /* Ensure text is above cards */
  white-space: nowrap; /* Prevent wrapping */
}

.gsap-section .card {
  position: absolute;
  width: 300px;
  height: 300px;
  background: gray; /* Placeholder background */
  border-radius: 20px;
  overflow: hidden;
  box-shadow: 0 10px 20px rgba(0,0,0,0.2); /* Added shadow */
}

/* Position cards relative to the wide wrapper-conclusion */
.gsap-section #card-1 {
  top: 50%;
  left: 120vw; /* Adjust positioning within the wide wrapper */
  transform: translateY(-50%);
}

.gsap-section #card-2 {
  top: 25%;
  left: 200vw;
  transform: translateY(-50%);
}

.gsap-section #card-3 {
  top: 45%;
  left: 280vw;
  transform: translateY(-50%);
}

.gsap-section #card-4 {
  top: 15%;
  left: 360vw;
  transform: translateY(-50%);
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
  .gsap-section .card {
    width: 200px;
    height: 200px;
  }
   .gsap-section .outro h1 {
     font-size: 30px;
   }
}
</style>