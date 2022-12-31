<script>
    import {closeModal} from 'svelte-modals'

    export let isOpen

    export let title
    export let message

    let okButton = 'px-6 py-2.5 bg-blue-600 text-white font-medium text-xs leading-tight uppercase rounded shadow-md\n' +
        ' hover:bg-blue-700 hover:shadow-lg focus:bg-red-700 focus:shadow-lg focus:outline-none focus:ring-0\n' +
        ' active:bg-blue-800 active:shadow-lg transition duration-150 ease-in-out';

</script>

{#if isOpen}

    <div class="bg-stone-800 text-black opacity-80 p-8 modal items-center justify-center fade fixed top-0 left-0 hidden w-full h-full outline-none overflow-y-auto"
          tabindex="-1" aria-hidden="true">
        <div class="modal-dialog flex relative w-auto pointer-events-none">
            <div
                    class="modal-content border-none shadow-lg relative flex flex-col w-full pointer-events-auto bg-white bg-clip-padding rounded-md outline-none text-current">
                <div
                        class="modal-header flex flex-shrink-0 items-center justify-between p-4 border-b border-gray-200 rounded-t-md">
                    <h5 class="text-xl font-medium leading-normal text-gray-800">{title}</h5>
                    <button type="button"
                            class="btn-close box-content w-4 h-4 p-1 text-black border-none rounded-none opacity-50 focus:shadow-none focus:outline-none focus:opacity-100 hover:text-black hover:opacity-75 hover:no-underline"
                            data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body relative p-4">
                    {message}
                </div>
                <div class="modal-footer flex flex-shrink-0 flex-wrap items-center justify-end p-4 border-t border-gray-200 rounded-b-md">
                    <button type="button" class="{okButton}" on:click={closeModal} data-bs-dismiss="modal">Ok</button>
                </div>
            </div>
        </div>
    </div>
{/if}


<style>
    .modal {
        display: flex;
    }

</style>
<!-- <div>
    <div
      class="modal pointer-events-none fixed w-full h-full top-0 left-0 flex items-center justify-center z-20"
    >
      <div
        class="modal-overlay absolute w-full h-full bg-stone-900 opacity-80"
      />

      <div
        class="modal-container bg-white w-11/12 md:max-w-md mx-auto rounded shadow-lg z-[200] overflow-y-auto"
      >
        <div
          class="modal-close absolute top-0 right-0 cursor-pointer flex flex-col items-center mt-4 mr-4 text-white text-sm z-50"
        >
          <span class="text-sm">(Esc)</span>
        </div>

        <div class="modal-content py-4 text-left px-6 bg-stone-700">
          <div class="flex justify-between items-center pb-3">
            <p class="text-2xl font-bold">Hi</p>
          </div>
          Hi
          Footer-->
          <!-- <div class="flex justify-end pt-2">
            <button
              class="modal-close px-4 bg-indigo-500 p-3 rounded-lg text-white hover:bg-indigo-400"
              on:click={closeModal}>Done!</button
            >
          </div>
        </div>
      </div>
    </div>
  </div> -->