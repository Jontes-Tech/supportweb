<script lang="ts">
  import PocketBase from "pocketbase";
  let record = {
    collectionId: "",
    collectionName: "",
    created: "",
    customer: "",
    id: "",
    problem: "",
    sessionId: "",
    state: "Ok",
    updated: "",
  };

  const pb = new PocketBase("https://supportdb.jontes.page");
  let problem = "";
  if (!localStorage.getItem("email")) {
    const email = prompt("Välkommen! Vad är din epostadress?").toLowerCase();
    const re =
      /(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))/;
    console.log(email);
    if (!email) {
      alert("Du måste skriva in en epost.");
    }
    if (!re.test(email)) {
      alert(
        'Det ser inte ut som en epost. Klicka på "ok" för att försöka igen.'
      );
    }
    if (email && re.test(email)) {
      localStorage.setItem("email", email);
    } else {
      window.location.reload();
    }
  }
  function calcDiff(expires: string) {
    return Math.floor((new Date(expires).getTime() - Date.now()) / 86400000);
  }
  let promise = fetch(
    "https://supportdb.jontes.page/api/collections/customers/records?mail=" +
      localStorage.getItem("email")
  ).then((x) => x.json());

  import { Modals, closeModal } from "svelte-modals";

  import { openModal } from "svelte-modals";
  import ModalComponent from "./ModalCompontent.svelte";

  function handleClick() {
    openModal(ModalComponent, {
      title: "Skaffa Support",
      message: `Få oändligt med support för endast 140 sek / månad för alla dina enheter. Rabatter kan ges via mejl (support@jontes.page). Hur betalar man? Swisha 140*antal månader du vill ha till 0703046627 och skriv i bes "Support ${localStorage.getItem(
        "email"
      )} Obegränsad"`,
    });
  }
  function generateId() {
    let pass = "";
    const str =
      "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
    for (let i = 1; i <= 32; i++) {
      var char = Math.floor(Math.random() * str.length + 1);
      pass += str.charAt(char);
    }
    return pass;
  }
  async function isOnline() {
    let date = new Date();
    const res = await fetch(
      "https://supportdb.jontes.page/api/collections/meta/records/mgjjj0pqaljkzsk"
    );
    const json = await res.json();
    if (json.online === "t") return true;
    if (json.online === "f") return false;
    if (date.getUTCDay() > 4) {
      if (date.getUTCHours() + 1 > 8 && date.getUTCHours() + 1 < 20) {
        return true;
      }
    }
    if (date.getUTCDay() < 4) {
      if (date.getUTCHours() + 1 > 16 && date.getUTCHours() + 1 < 20) {
        return true;
      }
    }
    return false;
  }
  import { onMount } from "svelte";

  let promise2;
  onMount(async () => {
    promise2 = isOnline();
  });
</script>

<svelte:window
  on:keydown={(e) => {
    if (e.key === "Escape") closeModal();
  }}
/>
<main class="bg-stone-800 p-4 text-white m-8">
  {#await promise}
    <p class="bg-stone-800">Laddar!</p>
  {:then data}
    <h1
      class="mb-4 text-4xl font-extrabold tracking-tight leading-none md:text-5xl lg:text-6xl text-white"
    >
      Tjenare
      <mark class="px-2 text-white rounded bg-blue-500"
        >{data.items[0].firstname}</mark
      >, {#await promise2 then online}
        {#if online}
          hur kan jag hjälpa dig idag?
        {:else}
          jag är offline, skriv ditt ärende så kontaktar jag dig.
        {/if}
      {/await}
    </h1>
    <p class="text-lg font-normal lg:text-xl text-stone-400">
      {#if calcDiff(data.items[0].expires) > -1}
        Du har {calcDiff(data.items[0].expires)} dag{calcDiff(
          data.items[0].expires
        ) !== 1
          ? "ar"
          : ""} kvar på din prenumeration <b>{data.items[0].plan}</b>.
        <button class="text-white font-bold" on:click={handleClick}
          >Skaffa.</button
        >
      {:else}
        Du har ingen prenumeration. <button
          class="text-white font-bold"
          on:click={handleClick}>Skaffa.</button
        >
      {/if}
    </p>
    {#if calcDiff(data.items[0].expires) > 0}
      <form
        on:submit={async (e) => {
          e.preventDefault();
          if (record.state === "Ok") {
            const data2 = {
              customer: data.items[0].id,
              problem: problem,
              state: "Meddelar expert",
              sessionId: generateId(),
            };
            fetch("https://api.jontes.page/new-support-ticket", {
              method: "POST",
            });

            record = await pb.collection("sessions").create(data2);
            console.table(record);
            console.log("Starting sub to record: " + record.id);
            pb.collection("sessions").subscribe(record.id, function (e) {
              if (e.record.state === "Startad")
                window.location.href =
                  "https://meet.jit.si/" + record.sessionId;
              record.state = e.record.state;
            });
          } else if (record.state === "Startad")
            window.location.href = "https://meet.jit.si/" + record.sessionId;
        }}
      >
        <div class="relative z-0">
          <input
            type="text"
            bind:value={problem}
            class="block w-full p-4 text-sm border rounded-lg bg-stone-700 border-stone-600 placeholder-stone-400 text-white focus:ring-blue-500 focus:border-blue-500"
            placeholder="Skriv ärende: (ex: Mejlen på min Seniordator funkar, men inte på min telefon)"
            required
          />
          <button
            type="submit"
            class="text-white absolute right-2.5 bottom-2.5 focus:ring-4 focus:outline-none font-medium rounded-lg text-sm px-4 py-2 bg-blue-600 hover:bg-blue-700 focus:ring-blue-800"
          >
            {#if record.state === "Meddelar expert"}
              <svg
                role="status"
                class="inline mr-3 w-4 h-4 text-white animate-spin"
                viewBox="0 0 100 101"
                fill="none"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
                  fill="#E5E7EB"
                />
                <path
                  d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
                  fill="currentColor"
                />
              </svg>
            {/if}
            {record.state}</button
          >
        </div>
      </form>
      {#if record.state === "Meddelar expert"}
        <p class="text-gray-400">
          Vänligen vänta fem minuter på svar. Om jag inte svarat då mejlar jag
          dig.
        </p>
      {/if}
    {/if}
  {:catch}
    <h1 class="text-4xl">Vi verkar ha problem!</h1>
    <p>
      Vänligen ring 0703046627
    </p>
  {/await}
  <Modals>
    <div slot="svelte-modal" on:click={handleClick} />
  </Modals>
</main>
