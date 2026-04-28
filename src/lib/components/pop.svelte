<script>
  import { fade, scale } from 'svelte/transition';
  import { secretLetters } from '$lib/data/letter.js';

  let activeType = null;
  let inputCode = ["", "", ""];

  const documents = [
    { id: 'id', label: 'Guest ID' },
    { id: 'letter', label: 'Letter' }
  ];

  function openModal(doc) {
    activeType = doc.id;
    inputCode = ["", "", ""];
  }

  function closeModal() {
    activeType = null;
  }

  // Ambil data surat berdasarkan kode
  $: currentLetter = secretLetters[inputCode.join('')];

  function handleInput(e, index) {
    if (e.target.value && index < 2) {
      e.target.nextElementSibling?.focus();
    }
  }
</script>

<div class="gallery">
  {#each documents as doc}
    <button on:click={() => openModal(doc)} class="btn-primary">
      <h2>{doc.label}</h2>
    </button>
  {/each}
</div>

{#if activeType}
  <div class="backdrop" on:click={closeModal} transition:fade>
    <div class="modal" on:click|stopPropagation transition:scale>
      <button class="close-btn" on:click={closeModal}>&times;</button>

      {#if activeType === 'id'}
        <div class="letter-component">
           <h2>Your Guest Access Card</h2>
           <img src="/idc.png" alt="Guest ID" />
           <p>Authorized Personnel Only</p>
        </div>
      
      {:else if activeType === 'letter'}
        {#if currentLetter}
          <div class="letter-component" transition:fade>
            <h2>{currentLetter.title}</h2>
            <img src={currentLetter.img} alt="Secret" />
            <p>{currentLetter.message}</p>
          </div>
        {:else}
          <div class="p-6 text-center">
            <h3 class="text-[#712262] font-bold mb-4">Input Code</h3>
            <div class="flex gap-2 justify-center">
              {#each [0, 1, 2] as i}
                <input 
                  type="text" maxlength="1" inputmode="numeric"
                  bind:value={inputCode[i]}
                  on:input={(e) => handleInput(e, i)}
                  class="code-input"
                />
              {/each}
            </div>
          </div>
        {/if}
      {/if}
    </div>
  </div>
{/if}

<style>
  /* Styling Komponen Surat */
  .letter-component {
    padding: 1rem;
    text-align: center;
  }

  .letter-component h2 {
    color: #712262;
    font-family: 'pgm', sans-serif; /* Pakai font kamu */
    font-size: 1.5rem;
    margin-bottom: 1rem;
  }

  .letter-component img {
    width: 100%;
    border-radius: 15px;
    margin-bottom: 1rem;
    border: 3px solid #712262;
  }

  .letter-component p {
    font-size: 1rem;
    color: #333;
    line-height: 1.5;
  }

  /* Style Pendukung */
  .btn-primary {
    width: 100%; padding: 0.5rem 1rem; background: #712262; 
    color: white; border-radius: 9999px; margin-bottom: 10px;
    font-size: 1.25rem;
  }
  
  .code-input {
    width: 3rem; height: 3.5rem; text-align: center;
    border: 2px solid #712262; border-radius: 10px;
    font-size: 1.5rem; font-weight: bold;
  }

  .backdrop {
    position: fixed; top: 0; left: 0; width: 100%; height: 100%;
    background: rgba(0, 0, 0, 0.85); display: flex; 
    justify-content: center; align-items: center; z-index: 1000;
  }

  .modal {
    position: relative; background: white; padding: 15px; 
    border-radius: 20px; max-width: 85%; width: 350px;
  }

  .close-btn {
    position: absolute; top: -10px; right: -10px; background: #ff4757;
    color: white; border: none; border-radius: 50%; width: 30px; height: 30px;
  }
</style>