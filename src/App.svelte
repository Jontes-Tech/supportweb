<script lang="ts">
  import { onMount } from "svelte";

  let data = [];
  let done = false;

  onMount(async () => {
    const email = prompt("Vad är din epost-adress?");
    const res = await fetch("http://localhost:3001/users/" + email);
    data = await res.json();
    done = true;
    const oneDay = 1000 * 60 * 60 * 24;
    const date1 = new Date();

    // Calculating the time difference between two dates
    const diffInTime = date1.getTime() - data.subbedUntil;

    // Calculating the no. of days between two dates
    const diffInDays = Math.floor(diffInTime / oneDay);
    console.log(diffInTime);
  });
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
    {#if data.realname === undefined && done === true}
      <h1>Du verkar inte än vara kund hos Seniordator</h1>
      <p>Du kan ändå chatta med supporten nere i högra hörnet!</p>
    {:else if data.realname === undefined && done === false}
      <p>Vänligen skriv in din epost i pop-up fönstret.</p>
    {:else}
      <h1>Tjenare, {data.realname}!</h1>
      <p>
        Du har planen <b>{data.plan}</b> och din prenumeration kommer att gå ut
        <b
          >om {diffInDays}
          {diffInDays !== 1 ? "dagar" : "dag"}</b
        >!
      </p>
    {/if}
  </div>
</main>
