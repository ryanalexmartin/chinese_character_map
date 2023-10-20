<template>
  <div id="app">
    <h1>Learned {{ learnedCount }} out of {{ totalCharacters }}</h1>
    <RecycleScroller 
      class="scroller"
      :items="characters"
      :item-size="50"
      key-field="id"
      v-slot="{ item }"
    >
      <CharacterCard :character="item" @update-learned="updateCharacterLearnedState"/>
    </RecycleScroller>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import { RecycleScroller } from 'vue-virtual-scroller';
import 'vue-virtual-scroller/dist/vue-virtual-scroller.css';
import CharacterCard from './components/CharacterCard.vue';
import charactersData from './data/variant-WordData.json';

export default {
  components: {
    CharacterCard,
    RecycleScroller
  },
  setup() {
    const characters = ref([]);
    const learnedCount = ref(0);
    const refreshKey = ref(0);

    onMounted(() => {
      characters.value = charactersData.map((char) => {
        return {
          id: char.serial,  
          char: char.word,
          learned: !!localStorage.getItem(char.word)
        };
      });

      learnedCount.value = characters.value.filter(c => c.learned).length;
    });

    const totalCharacters = charactersData.length;

    function forceRefresh() {
      refreshKey.value++;
    }

    function updateCharacterLearnedState({ id, learned }) {
      const characterToUpdate = characters.value.find(char => char.id === id);
      if (characterToUpdate) {
        characterToUpdate.learned = learned;
        if (learned) {
          learnedCount.value++;
        } else {
          learnedCount.value--;
        }
        forceRefresh();
      }
    }

    return {
      characters,
      learnedCount,
      totalCharacters,
      updateCharacterLearnedState
    };
  }
}
</script>

<style>
.vue-recycle-scroller.direction-vertical.scroller {
  height: 500px;  /* adjust this value based on your design */
  overflow-y: auto;
}
</style>
