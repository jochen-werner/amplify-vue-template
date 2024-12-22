<script setup lang="ts">
import '@/assets/main.css';
import { onMounted, ref } from 'vue';
import type { Schema } from '../../amplify/data/resource';
import { generateClient } from 'aws-amplify/data';

const client = generateClient<Schema>();

// create a reactive reference to the array of Players
const Players = ref<Array<Schema['Player']["type"]>>([]);

function listPlayers() {
  client.models.Player.observeQuery().subscribe({
    next: ({ items, isSynced }) => {
      Players.value = items
     },
  }); 
}

function createPlayer() {
  client.models.Player.create({
    content: window.prompt("Player content")
  }).then(() => {
    // After creating a new Player, update the list of Players
    listPlayers();
  });
}
    
// fetch Players when the component is mounted
 onMounted(() => {
  listPlayers();
});

</script>

<template>
  <main>
    <h1>My Players</h1>
    <button @click="createPlayer">+ new</button>
    <ul>
      <li 
        v-for="Player in Players" 
        :key="Player.id">
        {{ Player.name }}
      </li>
    </ul>
    <div>
      🥳 App successfully hosted. Try creating a new Player.
      <br />
      <a href="https://docs.amplify.aws/gen2/start/quickstart/nextjs-pages-router/">
        Review next steps of this tutorial.
      </a>
    </div>
  </main>
</template>
