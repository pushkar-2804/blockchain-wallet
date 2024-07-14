<script>
  import { afterUpdate } from "svelte";
  export let names;
  let users = names.map((user) => ({ ...user, edit: false, inputRef: null }));

  // Function to toggle edit mode and set input reference
  function toggleEdit(userId) {
    users = users.map((user) => {
      if (user.id === userId) {
        return { ...user, edit: !user.edit };
      }
      return user;
    });
  }

  // Lifecycle hook to focus on the input when edit is true
  afterUpdate(() => {
    users.forEach((user) => {
      if (user.edit && user.inputRef) {
        user.inputRef.focus();
      }
    });
  });
</script>

<div
  class="mt-10 pt-10 w-full max-w-xl p-12 mx-auto rounded-lg shadow-xl dark:bg-white/10 bg-white/30 ring-1 ring-gray-900/5 backdrop-blur-lg"
>
  <div class="flex items-center justify-between mb-4">
    <div class="space-y-1">
      <h2 class="text-xl font-semibold">List of Users</h2>
      <p class="text-sm text-gray-500">
        Fetched {names.length} users
      </p>
    </div>
  </div>
  <div class="divide-y divide-gray-900/5">
    {#each users as user (user.id)}
      <div class="flex items-center justify-between py-3">
        <div class="flex items-center space-x-4">
          <div class="flex items-center space-x-4">
            <p class="font-medium pt-1 leading-none">{user.name}</p>
            {#if user.edit}
              <!-- Email input field for editing -->
              <input
                class="font-medium pl-5 text-gray-500 pt-0"
                type="email"
                bind:this={user.inputRef}
                bind:value={user.email}
              />
            {:else}
              <!-- Display email when not editing -->
              <p class="font-medium pl-5 text-gray-500 pt-0">{user.email}</p>
            {/if}
            <!-- Toggle Edit Button -->
            <button on:click={() => toggleEdit(user.id)}
              >{user.edit ? "Cancel" : "Edit"}</button
            >
          </div>
        </div>
        <form method="POST" action="/profiles/delete">
          <input type="hidden" name="id" value={user.id} />
          <button type="submit">
            <img class="w-4 float-right" src="./trash-can.svg" alt="delete" />
          </button>
        </form>
        {#if user.edit}
          <!-- This form will now render correctly when user.edit is true -->
          <form method="POST" action="?/update">
            <input type="hidden" name="id" value={user.id} />
            <input type="hidden" name="name" value={user.name} />
            <input type="hidden" name="email" value={user.email} />
            <button type="submit">
              <img class="w-4 float-right" src="./edits.svg" alt="update" />
            </button>
          </form>
        {/if}
      </div>
    {/each}
  </div>
</div>
