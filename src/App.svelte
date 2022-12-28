<script lang="ts">
  import Chat from "./chat.svelte";
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
  let promise = fetch(
    "https://supportapi.jontes.page/users/" + localStorage.getItem("email")
  ).then((x) => x.json());
  function calcDiff(subbeduntil: number) {
    const now = Date.now() / 1000;
    const diff = subbeduntil - now;
    return Math.floor(diff / 86400);
  }
  import { dataset_dev } from "svelte/internal";
  import Modal from "./Modal.svelte";

  let isOpenModal = false;

  function openModal() {
    isOpenModal = true;
  }

  function closeModal() {
    isOpenModal = false;
  }

  function logout() {
    localStorage.clear();
    window.location.reload();
  }

  localStorage.setItem("callus.greeting_activated", "true");
</script>

<main>
  <div class="header">
    <img id="jonte" alt="Jonte" src="https://jontes.page/images/avatar.webp" />
    <div class="btn logo">Support</div>
    <button class="logout" on:click={logout}>Logga ut!</button>
  </div>
  <div id="body">
    {#await promise}
      <p>Loading!</p>
    {:then data}
      <h1>
        Tjenare, {data.realname
          ? data.realname
          : localStorage.getItem("email")}!
      </h1>
      {#if calcDiff(data.subbeduntil) > 0}
        <p>
          Din prenumeration <b>{data.plan}</b> kommer att gå ut om
          <b>{calcDiff(data.subbeduntil)}</b>
          {data.daysleft === 1 ? "dag" : "dagar"}.
        </p>
        <button class="moretime" on:click={openModal}>Lägg till mer tid!</button
        >
        <Modal {isOpenModal} on:closeModal={closeModal} />
      {:else}
        <p>Du har ingen aktiv prenumeration.</p>
        <button class="moretime" on:click={openModal}>Lägg till mer tid!</button
        >
        <Modal {isOpenModal} on:closeModal={closeModal} />
      {/if}
      <div style="opacity: 0">
        {localStorage.setItem(
          "call-us-auth-https%3A%2F%2Fholmgren.3cx.se",
          `{"name":"${
            data.realname ? data.realname : "Inte kund än"
          }","email":"${localStorage.getItem("email")}"}`
        )}
        {localStorage.setItem(
          "call-us-chat-active-https%3A%2F%2Fholmgren.3cx.seLiveChat681789",
          "true"
        )}
      </div>
      <call-us
        phonesystem-url="https://holmgren.3cx.se"
        style="position:fixed;font-size:16px;line-height:17px;z-index: 99999;--call-us-main-accent-color:#14A5FF;--call-us-main-background-color:#404040;--call-us-plate-background-color:#373737;--call-us-plate-font-color:#D9D9D9;--call-us-main-font-color:#FFFFFF;--call-us-agent-bubble-color:#FFFFFF10;right: 20px; bottom: 20px;"
        id="wp-live-chat-by-3CX"
        minimized="true"
        animation-style="noanimation"
        party="LiveChat681789"
        minimized-style="bubbleright"
        allow-call="true"
        allow-video="false"
        allow-soundnotifications="true"
        enable-mute="true"
        enable-onmobile="true"
        offline-enabled="true"
        enable="true"
        ignore-queueownership="false"
        authentication="both"
        show-operator-actual-name="true"
        aknowledge-received="true"
        gdpr-enabled="true"
        message-userinfo-format="both"
        message-dateformat="both"
        button-icon-type="default"
        greeting-visibility="none"
        greeting-offline-visibility="none"
        chat-delay="0"
        enable-direct-call="true"
        enable-ga="false"
        invite-message="Tjenixen %NAME%, hur kan jag hjälpa till?"
        authentication-message="Kan du ange ditt namn och epost?"
        unavailable-message="Jag är offline, vänligen skriv ett meddelande."
        offline-finish-message="Tack, jag har tagit emot ditt meddelande och kommer att kontakta dig så snart som möjligt."
        ending-message="Din session har precis avslutats, tveka inte att återkomma om det behövs!"
        first-response-message=""
        greeting-message=""
        greeting-offline-message="Hej, hur kan jag hjälpa dig?"
      />
      <script
        defer
        src="https://downloads-global.3cx.com/downloads/livechatandtalk/v1/callus.js"
        id="tcx-callus-js"
        charset="utf-8"
      ></script>
    {:catch error}
      <h1>Vi verkar ha problem!</h1>
      <p>
        Vårat kontosystem verkar lite småtrasigt just nu. Vänligen ring
        <b>070-3046627</b> istället.

        Felkod: <code>${error}</code>
      </p>
    {/await}
  </div>
</main>
