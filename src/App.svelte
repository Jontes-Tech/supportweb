<script lang="ts">
  if (!localStorage.getItem("email")) {
    localStorage.setItem("email", prompt("Vad är din epost-adress?"));
  }
  let promise = fetch(
    "https://supportapi.jontes.page/users/" + localStorage.getItem("email")
  ).then((x) => x.json());
  function calcDiff(subbeduntil: number) {
    console.log(subbeduntil);
    const now = Date.now() / 1000;
    const diff = subbeduntil - now;
    return Math.floor(diff / 86400);
  }
  import Modal from './Modal.svelte';

let isOpenModal = false;

function openModal() {
    isOpenModal = true;
}

function closeModal() {
    isOpenModal = false;
}
  
</script>

<main>
  <div class="header">
    <img
      id="jonte"
      alt="Jonte"
      src="https://cdn.jontes.page/cdn/avatar.min.webp"
    />
    <div class="btn logo">Support</div>
  </div>
  <div id="body">
    {#await promise}
      <p>Loading!</p>
    {:then data}
      <h1>Tjenare, {data.realname}!</h1>
      {#if calcDiff(data.subbeduntil) > 0}
        <p>
          Din prenumeration <b>{data.plan}</b> kommer att gå ut om
          <b>{calcDiff(data.subbeduntil)}</b>
          {data.daysleft === 1 ? "dag" : "dagar"}.
        </p>
        <button class='moretime' on:click={openModal}>Lägg till mer tid!</button>
        <Modal isOpenModal={isOpenModal} on:closeModal={closeModal} />
      {:else}
        <p>Du har ingen aktiv prenumeration.</p>
        <button class='moretime' on:click={openModal}>Lägg till mer tid!</button>
        <Modal isOpenModal={isOpenModal} on:closeModal={closeModal} />
      {/if}
    {:catch error}
      <h1>Vi verkar ha problem!</h1>
      <p>
        Vårat kontosystem verkar lite småtrasigt just nu, men du kan ändå chatta
        eller ringa nere i vänstra hörnet!
      </p>
    {/await}
  </div>
</main>
