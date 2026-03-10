<script>
import {defineComponent} from 'vue'
import DnD5eLayout from "components/DnD5e/DnD5eLayout.vue";
import {useCharacterStore} from "stores/character-store";
import {db} from "boot/firebase";
import {useAuthStore} from "stores/auth-store";
import Character from "src/models/Character";
import { getActivePinia } from "pinia";
import { useQuasar } from "quasar";

export default defineComponent({
  name: "GamePage",
  props: ['id'],
  components: {DnD5eLayout},
  data() {
    return {
      characterStore: useCharacterStore(),
      activeCharacter: {},
      permissionsDeniedDialog: false,
      $q: useQuasar(),
    }
  },
  methods: {
    showLoading () {
      this.$q.loading.show({
        message: 'Loading character. Hang on...',
      })
    },

    endLoading() {
      setTimeout(() => {this.$q.loading.hide()}, 100)
    },
    // abilityModifiers () {
    //   const stats = this.activeCharacter.abilityScoresTotal;
    //
    //   for (let key in stats) {
    //     if (stats.hasOwnProperty(key)) {
    //       let value = stats[key];
    //       stats[key] = Math.floor((value - 10) / 2);
    //       // console.log(key, stats[key])
    //     }
    //   }
    //   this.activeCharacter.abilityScoreModifiers = stats;
    //   this.characterStore.activeCharacter.abilityScoreModifiers = stats;
    //   // console.log(stats);
    // },

    // updateCharacter() {
    //   if (this.characterStore.activeCharacter.id !== this.id)
    //     this.characterStore.activateCharacter(this.id)
    //   this.$router.push({name: 'dnd5e-settings', params: {id: this.id}})
    // },

    redirectToCharacterSettings() {
      this.$router.push({name: 'dnd5e-settings', params: {id: this.id}})
    },

    activateCharacter(id) {
      this.characterStore.activateCharacter(id);
    },
  },
  mounted() {
    this.showLoading()
    this.activateCharacter(this.id);
    this.endLoading()
  },
  created() {},
})
</script>

<template>
  <div >
    <div class="name full-width bg-grey-5">
      <div class="characterAvatar">
        <q-avatar v-if="!!characterStore.activeCharacter.avatarURL" square color="grey" text-color="white" size="88px">
          <img :src="characterStore.activeCharacter.avatarURL" alt="Character Avatar"/>
        </q-avatar>
      </div>
      <div class="constrain q-mx-auto">
        <div class="name text-h3 relative-position">
          {{ characterStore.activeCharacter.name }}
          <q-btn v-if="characterStore.isUserCharacter" round flat class="absolute-right" icon="settings" size="16px" @click="redirectToCharacterSettings"><q-tooltip>Settings Page</q-tooltip></q-btn>
        </div>
        <div class="details text-h5 text-weight-light">
          {{ characterStore.activeCharacter.race }} | {{ characterStore.activeCharacter?.classData?.classLevelString }}
        </div>
      </div>
    </div>

    <div class="q-mt-lg flex flex-center">
      <DnD5eLayout v-if="characterStore.activeCharacter.gameType === 'DnD5e'" :data="characterStore.activeCharacter"/>
      <!--        <div class="debug">-->
      <!--          <q-btn @click="logChar()">Press me to get the characters</q-btn>-->
      <!--          <q-btn @click="selectCharacter('9v0qQSAGDo52AObkDdNU')">Press me to select a character</q-btn>-->
      <!--          <q-input v-model="newCharacter.name"></q-input>-->
      <!--          <q-btn @click="addCharacter(newCharacter)">Press me to add a character</q-btn>-->
      <!--        </div>-->
    </div>
  </div>
<!--  <div v-else class="loading text-h1 text-bold">Loading...</div>-->

  <!-- Permissions Denied Dialog -->
  <q-dialog v-model="permissionsDeniedDialog">
    <q-card>
      <q-card-section>
        <div class="text-h6 text-negative">Permission Denied</div>
      </q-card-section>

      <q-card-section class="q-pt-none">
        Character is not made publicly available.
      </q-card-section>

      <q-card-actions align="right">
        <q-btn flat label="OK" color="primary" v-close-popup />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<style scoped>
.constrain {
  max-width: 1200px;
  min-width: 1024px;
  width: 100%;
}
</style>
