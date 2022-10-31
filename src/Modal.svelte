
<script>
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  export let isOpenModal;

  function closeModal() {
      isOpenModal = false;
      dispatch('closeModal', { isOpenModal });
  }
</script>

<div id="background" style="--display: {isOpenModal ? 'block' : 'none'};" on:click={closeModal}></div>
<div id="modal" style="--display: {isOpenModal ? 'block' : 'none'};">
  <h3>Lägg till dagar på din Support-prenumeration!</h3>
  <p>Det kostar 140 sek i månaden ordinarie pris ALL INCLUSIVE. Rabbat kan ges. Här finns olika sätt att betala.

    Detta kan låta mycket men tänk på att detta inkluderar alternativ till det mesta (Microsoft office t.ex), Microsoft Windows-licenser o.s.v.

    Rabatter kan ges vid köp via Livechatten eller telefon (nere i högra hörnet) om du inte behöver ALL INCLUSIVE.
  </p>
  <li>Swisha 140kr gånger hur många månader du vill prenumerara till 070 304 66 27 med meddelandet "Support {localStorage.getItem('email')}".</li>
  <li>Om detta inte går kan du prata med livechatten nere i högra hörnet och diskutera alternativ.</li>
  <button on:click={closeModal}>Avbryt!</button>
</div>

<style>

  #modal {
      display: var(--display);
      color: white;
      position: fixed;
      z-index: 2;
      top: 50%;
      left: 50%;
      padding: 1em;
      transform: translate(-50%, -50%);
      background: #283618;
      border-radius: 1em;
  }
</style>
