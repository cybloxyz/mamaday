<script>
  import { menuData } from '$lib/data/menudata';
  import { page } from '$app/stores';
  import { language, currency } from '$lib/store';
  import { fade, fly, scale } from 'svelte/transition';
  import Navbar from '$lib/components/navbar.svelte';
  import { onMount } from 'svelte';

  $: itemId = $page.params.id;
  $: item = menuData.find(m => m.id === itemId);

  const rates = { id: 15000, en: 1 };
  $: displayPrice = item ? (item.price * ($currency === 'id' ? rates.id : rates.en)) : 0;
  $: priceLabel = $currency === 'id' ? 'Rp' : '$';

  const formatNumber = (num) => {
    return $currency === 'id' ? num.toLocaleString('id-ID') : num.toFixed(2);
  };

  // --- LOGIKA KODE RAHASIA ---
  let showDormModal = false;
  let selectedDorm = "";
  let savedCode = "";

  // Data kode (Beda asrama beda kode)
  const dormCodes = {
    "Hasanah": "H7X",
    "Ulya": "U4F",
    "Saadah": "S9Z",
    "Izzah": "I2B",
    "Najah": "N5K",
    "Thalha": "T8M",
    "Marhamah": "M1W",
    "Maimanah": "M3P",
    "Mukhtar": "M6G",
    "Yang lain": "Y0L"
  };

  onMount(() => {
    // Cek apakah sudah pernah klaim untuk item ini
    const existing = localStorage.getItem(`secret_code_${itemId}`);
    if (existing) savedCode = existing;
  });

  function handleClaim() {
    if (selectedDorm) {
      const code = dormCodes[selectedDorm];
      localStorage.setItem(`secret_code_${itemId}`, code);
      savedCode = code;
      showDormModal = false;
    }
  }
</script>

<Navbar />

<div class="min-h-screen pt-28 md:pt-40 pb-10 px-6 flex justify-center items-center" 
     style="background-color: #facfa2; background-image: url('/mmday.png');">
  
  {#if item}
    <div in:fade={{ duration: 500 }} 
         class="max-w-6xl w-full grid grid-cols-1 lg:grid-cols-12 gap-8 bg-white/90 backdrop-blur-md rounded-[40px] border-4 border-[#380d07] overflow-hidden shadow-2xl">
      
      <div class="lg:col-span-7 p-8 md:p-12 border-b-4 lg:border-b-0 lg:border-r-4 border-[#380d07] overflow-y-auto max-h-[80vh]">
        <h3 class="pgm text-3xl mb-6 text-[#713822] underline decoration-double">INGREDIENTS</h3>
        <ul class="grid grid-cols-1 md:grid-cols-2 gap-3 mb-10">
          {#each item.recipe.ingredients[$language] as ing}
            <li class="bby text-xl flex items-center gap-2">
              <span class="w-2 h-2 bg-[#713822] rounded-full"></span>
              {ing}
            </li>
          {/each}
        </ul>

        <h3 class="pgm text-3xl mb-6 text-[#713822] underline decoration-double">INSTRUCTIONS</h3>
        <ol class="space-y-6">
          {#each item.recipe.instructions[$language] as step, i}
            <li class="flex gap-4" in:fly={{ x: -20, delay: i * 100 }}>
              <span class="pix text-2xl bg-[#713822] text-white w-10 h-10 flex items-center justify-center rounded-full flex-shrink-0">
                {i + 1}
              </span>
              <p class="bby text-xl leading-relaxed text-gray-800">{step}</p>
            </li>
          {/each}
        </ol>
      </div>

      <div class="lg:col-span-5 p-8 md:p-12 flex flex-col items-center text-center bg-[#fdf2e9]">
        <div class="relative group mb-8">
          <div class="absolute inset-0 bg-[#713822] rounded-3xl rotate-3 group-hover:rotate-0 transition-transform"></div>
          <img 
            src={item.image} 
            alt={item.name[$language]} 
            class="relative w-64 h-64 object-contain drop-shadow-2xl bg-white p-4 rounded-3xl border-4 border-[#380d07]"
          />
        </div>

        <div class="inline-block px-4 py-1 bg-[#713822] text-white pix text-sm rounded-full mb-4">
          {item.rarity}
        </div>

        <h1 class="pgm text-5xl mb-4 text-[#380d07] uppercase leading-tight">
          {item.name[$language]}
        </h1>

        <p class="bby text-xl text-gray-600 mb-8 italic">
          "{item.description[$language]}"
        </p>

        <div class="w-full mb-6">
          {#if savedCode}
            <div class="bg-[#380d07] p-4 rounded-2xl border-2 border-[#713822] text-white" in:scale>
              <span class="pix text-[10px] block opacity-70">SECRET CODE UNLOCKED</span>
              <span class="bby text-2xl tracking-widest">{savedCode}</span>
            </div>
          {:else}
            <button 
              on:click={() => showDormModal = true}
              class="w-full py-3 bg-[#712262] text-white pgm text-lg rounded-full hover:bg-[#560d46] transition-all border-2 border-white shadow-md">
              CLAIM SECRET CODE
            </button>
          {/if}
        </div>

        <div class="mt-auto w-full border-t-4 border-dashed border-[#713822] pt-6 mb-6">
          <span class="pix text-lg text-[#713822] block mb-1">MARKET PRICE</span>
          <span class="pix text-4xl text-[#380d07]">
            {priceLabel} {formatNumber(displayPrice)}
          </span>
        </div>
        
        <a href="/menu" 
           class="w-full px-10 py-4 bg-[#380d07] text-white pgm text-xl rounded-full hover:scale-105 transition-all shadow-lg text-center">
          BACK TO MENU
        </a>
      </div>

    </div>
  {:else}
    <div class="text-center bg-white/80 p-10 rounded-3xl border-4 border-[#380d07]">
      <h1 class="pgm text-4xl">RECIPE NOT FOUND!</h1>
      <a href="/menu" class="text-[#713822] underline pix mt-4 block">Return to Menu</a>
    </div>
  {/if}
</div>

{#if showDormModal}
  <div class="backdrop" on:click={() => showDormModal = false} transition:fade>
    <div class="modal max-h-[90vh] flex flex-col" on:click|stopPropagation transition:scale>
      <h2 class="pgm text-2xl text-[#380d07] mb-2 text-center uppercase">Choose Name</h2>
      <p class="pix text-[10px] text-center mb-4">Select your name to reveal the password</p>
      
      <div class="grid grid-cols-2 gap-2 mb-6 overflow-y-auto pr-2">
        {#each Object.keys(dormCodes) as name}
          <button 
            on:click={() => selectedDorm = name}
            class="py-2 px-2 border-2 pgm text-sm rounded-xl transition-all {selectedDorm === name ? 'bg-[#713822] text-white border-[#713822]' : 'border-[#380d07] text-[#380d07]'}">
            {name}
          </button>
        {/each}
      </div>

      {#if selectedDorm}
        <button 
          on:click={handleClaim}
          in:fade
          class="w-full py-3 bg-[#380d07] text-white pgm text-xl rounded-xl hover:bg-black transition-all shadow-lg">
          GET PASSWORD
        </button>
      {/if}
    </div>
  </div>
{/if}

<style>
  .pix { font-family: 'pix'; }
  .bby { font-family: 'bby'; }
  .pgm { font-family: 'pgm'; }

  .backdrop {
    position: fixed; top: 0; left: 0; width: 100%; height: 100%;
    background: rgba(56, 13, 7, 0.8); display: flex; justify-content: center; align-items: center; z-index: 1000;
    backdrop-filter: blur(5px);
  }

  .modal {
    background: #facfa2; padding: 2rem; border-radius: 30px; border: 4px solid #380d07;
    width: 90%; max-width: 350px;
  }

  div::-webkit-scrollbar { width: 8px; }
  div::-webkit-scrollbar-track { background: transparent; }
  div::-webkit-scrollbar-thumb { background: #713822; border-radius: 10px; }
</style>