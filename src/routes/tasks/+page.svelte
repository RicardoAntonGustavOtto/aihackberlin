<!-- @format -->
<script>
  import { events } from "$lib/stores/eventStore.js"; // Import the store

  // Filter only task type events using the store
  $: taskEvents = $events.filter((event) => event.type === "Task");
</script>

<main>
  <div class="container">
    <h1>Tasks</h1>

    <div class="task-list">
      {#if taskEvents.length === 0}
        <p class="empty-state">No tasks yet</p>
      {:else}
        {#each taskEvents as task}
          <div class="task-item" style="background-color: {task.color}">
            <span>{task.title}</span>
            <button
              class="delete-btn"
              on:click={() => {
                $events = $events.filter((e) => e.id !== task.id);
              }}
            >
              ×
            </button>
          </div>
        {/each}
      {/if}
    </div>

    <a href="/" class="back-button" aria-label="Back to calendar">
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
          d="M15 19l-7-7 7-7"
        />
      </svg>
    </a>
  </div>
</main>

<style>
  main {
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 2rem;
    background-color: #f8fafc;
  }

  .container {
    width: 100%;
    max-width: 600px;
    position: relative;
  }

  h1 {
    text-align: center;
    color: #2c3e50;
    font-weight: 500;
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  .task-list {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }

  .task-item {
    padding: 1rem;
    border-radius: 8px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    transition: transform 0.2s ease;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
  }

  .task-item:hover {
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

  .empty-state {
    text-align: center;
    color: #64748b;
    padding: 2rem;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
  }

  .back-button {
    position: fixed;
    top: 2rem;
    left: 2rem;
    background: white;
    padding: 0.75rem;
    border-radius: 50%;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    transition: transform 0.2s ease;
  }

  .back-button:hover {
    transform: translateX(-4px);
  }
</style>
