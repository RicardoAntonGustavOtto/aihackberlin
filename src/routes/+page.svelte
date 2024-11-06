<!-- @format -->
<script>
  import { events } from "$lib/stores/eventStore.js";

  const timeBlocks = [
    { id: "morning", title: "Morning", color: "#E6F3FF" },
    { id: "day", title: "Day", color: "#fff5ba" },
    { id: "evening", title: "Evening", color: "#E6EEFF" },
  ];

  let showModal = false;
  let newEvent = {
    title: "",
    color: "",
    type: "",
  };

  const pastelColors = [
    "#BAFFC9", // pastel green
    "#FFFFBA", // pastel yellow
    "#FFB347", // deeper orange
    "#FF6B6B", // deeper red
  ];

  const eventTypes = ["Morning", "Day", "Evening", "Task"];
  let currentStep = 1;

  function openModal() {
    showModal = true;
    currentStep = 1;
  }

  function closeModal() {
    showModal = false;
    currentStep = 1;
    newEvent = {
      title: "",
      color: "",
      type: "",
    };
  }

  function nextStep() {
    currentStep += 1;
  }

  function prevStep() {
    currentStep -= 1;
  }

  function addEvent() {
    $events = [...$events, { ...newEvent, id: Date.now() }];
    closeModal();
  }

  function getEventsForBlock(blockId) {
    return $events.filter((event) => event.type.toLowerCase() === blockId);
  }
</script>

<main>
  <h1>Daily Planning</h1>

  <div class="calendar-container">
    {#each timeBlocks as block}
      <div class="time-block" style="background-color: {block.color}">
        <h2>{block.title}</h2>
        <div class="block-content">
          {#each getEventsForBlock(block.id) as event}
            <div class="event-item" style="background-color: {event.color}">
              <span>{event.title}</span>
              <button
                class="delete-btn"
                on:click={() => {
                  $events = $events.filter((e) => e.id !== event.id);
                }}
              >
                ×
              </button>
            </div>
          {/each}
        </div>
      </div>
    {/each}
  </div>

  <div class="fixed bottom-4 right-4 flex gap-4">
    <a
      href="/tasks"
      class="bg-blue-200 hover:bg-blue-300 text-blue-800 rounded-full p-4 shadow-lg"
      aria-label="View tasks"
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        class="h-6 w-6"
        fill="none"
        viewBox="0 0 24 24"
        stroke="currentColor"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2"
        />
      </svg>
    </a>

    <button
      class="bg-blue-200 hover:bg-blue-300 text-blue-800 rounded-full p-4 shadow-lg"
      on:click={openModal}
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        class="h-6 w-6"
        fill="none"
        viewBox="0 0 24 24"
        stroke="currentColor"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M12 4v16m8-8H4"
        />
      </svg>
    </button>
  </div>

  {#if showModal}
    <div
      class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4"
    >
      <div class="bg-white rounded-lg p-6 w-full max-w-md">
        <div class="flex justify-between mb-6">
          {#each [1, 2, 3] as step}
            <div
              class="w-24 h-1 rounded"
              class:bg-blue-500={step <= currentStep}
              class:bg-gray-200={step > currentStep}
            ></div>
          {/each}
        </div>

        {#if currentStep === 1}
          <h2 class="text-xl font-bold mb-4">Name Your Event</h2>
          <input
            type="text"
            placeholder="Enter event title"
            bind:value={newEvent.title}
            class="w-full p-3 border rounded mb-4 text-lg"
            autofocus
          />
          {#if newEvent.title}
            <div class="flex justify-end">
              <button
                class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
                on:click={nextStep}
              >
                Next
              </button>
            </div>
          {/if}
        {:else if currentStep === 2}
          <h2 class="text-xl font-bold mb-4">Choose Color</h2>
          <div class="grid grid-cols-2 gap-4">
            {#each pastelColors as color}
              <button
                class="p-8 rounded-lg border-2 transition-transform hover:scale-105"
                style="background-color: {color}"
                class:border-blue-500={newEvent.color === color}
                on:click={() => {
                  newEvent.color = color;
                  nextStep();
                }}
              ></button>
            {/each}
          </div>
        {:else if currentStep === 3}
          <h2 class="text-xl font-bold mb-4">Choose Time Block</h2>
          <div class="grid grid-cols-2 gap-3">
            {#each eventTypes as type}
              <button
                class="p-4 rounded border text-center transition-colors"
                class:bg-blue-100={newEvent.type === type}
                class:border-blue-500={newEvent.type === type}
                on:click={() => {
                  newEvent.type = type;
                  if (newEvent.title && newEvent.color && newEvent.type) {
                    addEvent();
                  }
                }}
              >
                {type}
              </button>
            {/each}
          </div>
        {/if}

        <div class="flex justify-between mt-6">
          <button
            class="px-4 py-2 text-gray-600 hover:bg-gray-100 rounded"
            on:click={currentStep === 1 ? closeModal : prevStep}
          >
            {currentStep === 1 ? "Cancel" : "Back"}
          </button>
        </div>
      </div>
    </div>
  {/if}
</main>

<style>
  main {
    max-width: 1000px;
    margin: 0 auto;
    padding: 2rem;
    font-family:
      system-ui,
      -apple-system,
      sans-serif;
  }

  h1 {
    text-align: center;
    color: #2c3e50;
    font-weight: 500;
    margin-bottom: 2rem;
  }

  .calendar-container {
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
  }

  .time-block {
    padding: 1.5rem;
    border-radius: 12px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
    transition: transform 0.2s ease;
  }

  .time-block:hover {
    transform: translateY(-2px);
  }

  h2 {
    color: #34495e;
    font-weight: 500;
    margin: 0 0 1rem 0;
    font-size: 1.25rem;
  }

  .block-content {
    min-height: 100px;
    display: flex;
    flex-direction: column;
  }

  .event-item {
    padding: 0.75rem;
    margin: 0.5rem 0;
    border-radius: 8px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    transition: transform 0.2s ease;
  }

  .event-item:hover {
    transform: translateX(4px);
  }

  .delete-btn {
    background: none;
    border: none;
    color: #666;
    font-size: 1.25rem;
    cursor: pointer;
    padding: 0 0.5rem;
    opacity: 0.7;
  }

  .delete-btn:hover {
    opacity: 1;
    color: #ff4444;
  }
</style>
