<script>
  import { onMount } from 'svelte';
  import { fade, fly } from 'svelte/transition';
  import { language, currency, manualRates } from '$lib/store';
  import Navbar from '$lib/components/navbar.svelte';
  import Bentobox from '$lib/components/bentobox.svelte'; 
  import Pop from '$lib/components/pop.svelte'; 
  import moon from '$lib/assets/moon.png';
  import './layout.css';
  
  let isLoading = true;

  // Konten teks (pindahan dari thebento.jsx)
  const content = {
    say: { id: `SELAMAT HARI IBU!`, en: `HAPPY MOTHER'S DAY!` },
    say2: { 
      id: `untuk seluruh peran ibu di dunia`, 
      en: `ready to take cooking test?` 
    },
    say3: { id: `untuk bermain mini game klik tombol 'kuis'`, en: `click the 'cook' button!` },
    staff: { id: `TERATAS!`, en: `TOP!` },
  };

  onMount(() => {
    // 1. Logic Loading Screen (2 detik)
    setTimeout(() => {
      isLoading = false;
    }, 1000);

    // 2. Logic Auto Detect Lokasi (pindahan dari App.jsx)
    fetch(`https://ipapi.co/json/`)
      .then(r => r.json())
      .then(data => {
        if (data.country_code.toLowerCase() === "id") {
          $language = "id";
          $currency = "id";
        }
      })
      .catch(() => console.log("Gagal deteksi lokasi, pakai default EN/USD"));
  });
</script>

{#if isLoading}
  <div class="fixed inset-0 flex flex-col items-center justify-center bg-[#fed0f1] z-999" transition:fade>
    <img src="/bunn.png" alt="Loading..." class="w-48 h-48 object-contain" />
    <h1 class="text-3xl text-[#a43680]">loading!</h1>
  </div>
{:else}
  <div class="flat min-h-screen relative" style="background-color: #facfa2; background-image: url('/mmday.png');">

<Navbar/>

    <div class="bg-white/10 backdrop-blur-sm min-h-screen w-full">
      
      <section id="hero" class="pt-38 md:pt-50 pb-20 flex flex-col justify-center items-center">
      <img src={moon}/>
          <div class="text-center px-4">
            <h1 class="text-border text-7xl md:text-9xl mb-8 drop-shadow-lg">
              {content.say[$language]}
            </h1>
            <p class="text-lg md:text-2xl mb-6 text-[#a43680] font-medium">
              {content.say2[$language]}
            </p>
            <p class="text-sm md:text-lg mb-8 text-[#bb4494] font-medium">
              {content.say3[$language]}
            </p>
        </div>
        <Pop/>
      </section>

 <!--     <section id="best" class="mt-24 pb-20">
        <div class="flex justify-center items-center mb-12">
          <h1 class="bg-[#713822] text-white rounded-full px-10 py-4 text-center text-3xl md:text-5xl pgm shadow-xl">
            {content.staff[$language]}
          </h1>
        </div>
        <Bentobox />
      </section>
-->

    </div>
  </div>
{/if}

<style>
  .flat {
    background-attachment: fixed;
    background-size: cover;
  }
  .pix { font-family: 'pix';}
</style>