<script lang="ts">
  import { signIn, signOut } from '@auth/sveltekit/client';
  import Icon from '@iconify/svelte';
  import { fly } from 'svelte/transition';
  import { linear } from 'svelte/easing';
  import { goto } from '$app/navigation';

  let { data } = $props();
  let dropdown = $state(false);

  const menuItems = [
    { name: 'Kezdőlap', navFn: () => goto('/'), icon: 'fa-solid:home' },
    { name: 'Profil', navFn: () => goto('/profile'), icon: 'fa-solid:user', auth: true },
    { name: 'Kijelentkezés', navFn: () => signOut(), icon: 'mdi:exit-run', auth: true }
  ];
</script>

<div class="flex flex-col md:pb-4">
  <div
    class="ml-auto mr-auto flex w-full max-w-screen-xl flex-wrap items-center justify-between bg-opacity-80 p-2 px-4 transition-all ease-linear md:bg-black md:bg-opacity-30 md:p-4 md:shadow-lg xl:rounded-b-xl"
    class:bg-black={dropdown}
  >
    <div class="flex items-center gap-4">
      <a href="/" class="text-2xl font-bold">Helyettesítés</a>
      <ul class="hidden w-full items-center gap-4 md:flex">
        {#each menuItems as item}
          {#if (item.auth && data.session) || !item.auth}
            <li class="w-full">
              <button
                onclick={item.navFn}
                class="flex w-full items-center gap-1 rounded-lg p-2 transition-colors hover:bg-gray-200 hover:text-black"
              >
                <Icon icon={item.icon} />
                <span>{item.name}</span>
              </button>
            </li>
          {/if}
        {/each}
      </ul>
    </div>
    <div class="hidden items-center gap-4 md:flex md:w-auto">
      {#if data.session}
        <img
          src={data.session.user?.image}
          alt={`${data.session.user.name}'s profile picture'`}
          class="w-10 rounded-full border-2 border-emerald-900"
        />
      {:else}
        <button
          onclick={() => signIn('microsoft-entra-id')}
          class="bg-from-petrik-1 bg-to-petrik-2 flex items-center gap-1 rounded-lg bg-opacity-20 bg-gradient-to-br bg-size-200 bg-pos-0 px-4 py-2 text-white transition-all duration-500 hover:bg-pos-100"
        >
          <Icon icon="mdi:login" class="h-5 w-5" />
          <span>Bejelentkezés</span>
        </button>
      {/if}
    </div>
    <div class="inline-flex items-center md:hidden">
      <button class="text-2xl" onclick={() => (dropdown = !dropdown)}>
        <span class="sr-only">Open dropdown</span>
        <Icon icon="fa-solid:bars" />
      </button>
    </div>
  </div>
  {#if dropdown}
    <div transition:fly={{ duration: 150, easing: linear }}>
      <ul class="absolute flex w-full flex-col gap-3 bg-black bg-opacity-80 p-2 font-semibold">
        {#each menuItems as item}
          {#if (item.auth && data.session) || !item.auth}
            <li>
              <button
                onclick={item.navFn}
                class="flex items-center gap-1 rounded-lg p-2 transition-colors hover:bg-gray-200 hover:text-black"
              >
                <Icon icon={item.icon} />
                <span>{item.name}</span>
              </button>
            </li>
          {/if}
        {/each}
        {#if !data.session}
          <li>
            <button
              onclick={() => signIn('microsoft-entra-id')}
              class="flex items-center gap-1 rounded-lg p-2 transition-colors hover:bg-gray-200 hover:text-black"
            >
              <Icon icon="mdi:login" />
              <span>Bejelentkezés</span>
            </button>
          </li>
        {/if}
      </ul>
    </div>
  {/if}
</div>
