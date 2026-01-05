<script setup lang="ts">
import { onMounted, onUnmounted, ref } from 'vue';
import gsap from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import { useHead } from 'nuxt/app';

// SEO Meta Data (Nuxt 4 Way)
useHead({
  title: 'HANDZAX - Luxury Perfume',
  meta: [
    { name: 'description', content: 'Rasakan kemewahan aroma Dunhill Blue dan HMNS Alpha.' }
  ]
})

// Register Plugin GSAP
// Kita lakukan di dalam onMounted agar aman dari SSR (Server Side Rendering)
gsap.registerPlugin(ScrollTrigger);

const mainContainer = ref<HTMLElement | null>(null);
let ctx: gsap.Context;

onMounted(() => {
  // GSAP Context untuk memudahkan cleanup (pembersihan memori)
  ctx = gsap.context(() => {

    // Ambil semua section
    const sections = gsap.utils.toArray<HTMLElement>('.product-section');

    sections.forEach((section) => {
      // 1. Efek Pinning (Gambar diam saat discroll)
      ScrollTrigger.create({
        trigger: section,
        start: "top top",
        end: "+=120%", // Sedikit lebih panjang biar user puas lihat gambarnya
        pin: true,
        scrub: true,
        anticipatePin: 1 // Biar lebih smooth di browser HP
      });

      // 2. Animasi Elemen di dalam section (Teks & Tombol)
      // Teks Judul
      gsap.from(section.querySelector('.animate-title'), {
        y: 100,
        opacity: 0,
        duration: 1.5,
        ease: "power3.out",
        scrollTrigger: {
          trigger: section,
          start: "top 60%", // Mulai animasi saat section masuk 60% layar
          end: "top 20%",
          scrub: 1,
        }
      });

      // Tombol
      gsap.from(section.querySelector('.animate-btn'), {
        y: 50,
        opacity: 0,
        delay: 0.2, // Muncul sedikit setelah judul
        scrollTrigger: {
          trigger: section,
          start: "top 50%",
          end: "center center",
          scrub: 1,
        }
      });

      // Animasi Partikel saat Scroll
      gsap.to(".particle", {
        y: (i) => -100 - (i * 20), // Setiap partikel naik dengan jarak berbeda (parallax)
        opacity: (i) => 0.1 + (Math.random() * 0.5), // Variasi transparansi
        ease: "none",
        scrollTrigger: {
          trigger: mainContainer.value,
          start: "top top",
          end: "bottom bottom",
          scrub: 1.5, // Memberikan efek "lembut" saat berhenti scroll
        }
      });

      // Tambahkan efek "Floating" (Terus bergerak pelan meski tidak discroll)
      gsap.to(".particle", {
        x: "random(-20, 20)",
        duration: "random(2, 4)",
        repeat: -1,
        yoyo: true,
        ease: "sine.inOut"
      });
    });

  }, mainContainer.value as Element); // Scope selector ke mainContainer
});

onUnmounted(() => {
  if (ctx) ctx.revert(); // Wajib di Nuxt agar tidak error saat pindah halaman
});
</script>

<template>
  <div ref="mainContainer" class="bg-black text-white overflow-hidden relative">

    <!-- Partikel -->
    <div class="fixed inset-0 pointer-events-none z-20">
      <div v-for="n in 20" :key="n" :class="`particle p-${n} absolute w-1 h-1 bg-white/30 rounded-full`" :style="{
        left: Math.random() * 100 + 'vw',
        top: Math.random() * 100 + 'vh'
      }">
      </div>
    </div>

    <nav
      class="fixed top-0 left-0 w-full z-50 p-6 flex justify-between items-center mix-blend-difference pointer-events-none">
      <span class="text-xl font-serif font-bold tracking-widest text-white pointer-events-auto">HANDZAX</span>
    </nav>

    <section
      class="product-section relative h-screen w-full flex flex-col justify-end items-center pb-20 overflow-hidden">
      <div class="absolute inset-0 z-0">
        <img src="/images/dunhill-blue.jpg" alt="Dunhill Blue Background"
          class="w-full h-full object-cover object-center scale-105" />
        <div class="absolute inset-0 bg-gradient-to-t from-black via-black/40 to-transparent"></div>
      </div>

      <div class="relative z-10 text-center px-6 max-w-md mx-auto">
        <div class="animate-title">
          <p class="text-blue-300 tracking-[0.3em] text-xs font-bold uppercase mb-3">Signature Scent</p>
          <h1 class="text-5xl md:text-6xl font-serif text-white mb-4 drop-shadow-2xl">Dunhill Blue</h1>
          <p class="text-gray-300 text-sm md:text-base leading-relaxed mb-8 font-light">
            Aroma langit cerah yang menenangkan. Kombinasi sempurna untuk pria maskulin yang penuh misteri.
          </p>
        </div>

        <div class="animate-btn">
          <button
            class="group relative px-8 py-3 rounded-full overflow-hidden bg-blue-900/30 backdrop-blur-md border border-blue-400/30 transition-all active:scale-95">
            <span class="relative z-10 text-xs tracking-widest font-bold text-blue-50 group-hover:text-white">BELI
              SEKARANG</span>
            <div
              class="absolute inset-0 bg-blue-600/50 transform scale-x-0 group-hover:scale-x-100 transition-transform origin-left duration-500">
            </div>
          </button>
        </div>
      </div>
    </section>

    <section
      class="product-section relative h-screen w-full flex flex-col justify-end items-center pb-20 overflow-hidden">
      <div class="absolute inset-0 z-0">
        <img src="/images/hmns-alpha.jpg" alt="HMNS Alpha Background"
          class="w-full h-full object-cover object-center scale-105" />
        <div class="absolute inset-0 bg-gradient-to-t from-black via-black/50 to-transparent"></div>
      </div>

      <div class="relative z-10 text-center px-6 max-w-md mx-auto">
        <div class="animate-title">
          <p class="text-amber-400 tracking-[0.3em] text-xs font-bold uppercase mb-3">Best Seller</p>
          <h1 class="text-5xl md:text-6xl font-serif text-amber-50 mb-4 drop-shadow-2xl">HMNS Alpha</h1>
          <p class="text-amber-100/80 text-sm md:text-base leading-relaxed mb-8 font-light">
            Karakter kuat yang tak terlupakan. Pancarkan aura dominan dengan sentuhan emas.
          </p>
        </div>

        <div class="animate-btn">
          <button
            class="group relative px-8 py-3 rounded-full overflow-hidden bg-amber-900/30 backdrop-blur-md border border-amber-400/30 transition-all active:scale-95">
            <span class="relative z-10 text-xs tracking-widest font-bold text-amber-50 group-hover:text-white">BELI
              SEKARANG</span>
            <div
              class="absolute inset-0 bg-amber-600/50 transform scale-x-0 group-hover:scale-x-100 transition-transform origin-left duration-500">
            </div>
          </button>
        </div>
      </div>
    </section>

  </div>
</template>

<style scoped>
/* Font Playfair Display untuk kesan Luxury */
@import url('https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,700;1,400&display=swap');

.font-serif {
  font-family: 'Playfair Display', serif;
}

/* Memastikan tidak ada scroll horizontal liar di HP */
.product-section {
  width: 100vw;
  overflow: hidden;
}
</style>