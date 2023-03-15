<script setup lang="ts">
import { ref} from "vue";
// import {useMidi} from "../composables/useMidi";
import { Chord, Scale } from 'tonal';
// import {Piano} from "@tonejs/piano";
import {useMidi} from "./composables/useMidi";
const {midiOutputs, dawMidiOutput, dawMidiInput, midiInputs, onPedalEvent} = useMidi(true)
/* const piano = new Piano({
  velocities: 1,
})
piano.load().then(() => {
  console.log('piano loaded')
})
piano.toDestination()*/
const selectedScaleTypes = ref(['major', 'minor'])
const selectedRange = ref([2, 3, 4, 5])
const playedChord = ref(Chord.getChord('major', 'C'))
const visibleChord = ref(false)

const generateRandomChord = () => {
  visibleChord.value = false
  const chromaticScale = Scale.get('C chromatic');
  const notes = chromaticScale.notes;
  const randomNote = notes[Math.floor(Math.random() * notes.length)];
  const randomScaleType = selectedScaleTypes.value[Math.floor(Math.random() * selectedScaleTypes.value.length)];
  const randomChord = Chord.getChord(randomScaleType, randomNote);
  const randomOctave = selectedRange.value[Math.floor(Math.random() * selectedRange.value.length)];
  const randomNoteChord = randomChord.notes[Math.floor(Math.random() * randomChord.notes.length)]
  const randomInversion = Chord.getChord(randomScaleType, randomNote + randomOctave);
  console.log(randomInversion)
  playedChord.value = randomInversion
  dawMidiOutput.value?.channels[1].playNote(randomInversion.notes, { duration: 1000 })
}

const showChord = () => {
  visibleChord.value = true
}

const replayChord = () => {
  dawMidiOutput.value?.channels[1].playNote(playedChord.value.notes, { duration: 1000 })
}

</script>

<template>
  <div class="pedalboard grid grid-cols-1 grid-rows-[auto_auto_1fr]">
    <div class="top-bar flex justify-between items-center p-4 gap-4">
      <div class="w-full">
        <label for="midi-input" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">
          Simulator Midi Out
        </label>
        <select
            class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
            v-model="dawMidiInput">
          <option v-for="input in midiInputs" :key="input" :value="input">{{ input.name }}</option>
        </select>
      </div>
      <div class="w-full">
        <label for="midi-output" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">
          Simulator Midi In
        </label>
        <select
            class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
            v-model="dawMidiOutput">
          <option v-for="output in midiOutputs" :key="output" :value="output">{{ output.name }}</option>
        </select>
      </div>
    </div>
    <div class="preset-bar flex justify-center items-center h-16 mb-4 text-4xl"><template  v-if="visibleChord">{{ playedChord.name }}</template></div>
    <div class="grid-layout flex grid justify-center grid-cols-3 gap-4 p-4">
      <button
          class="text-white bg-gray-800 hover:bg-gray-900 focus:outline-none focus:ring-4 focus:ring-gray-300 font-medium rounded-lg text-sm px-5 py-2.5 mr-2 mb-2 dark:bg-gray-800 dark:hover:bg-gray-700 dark:focus:ring-gray-700 dark:border-gray-700"
          @click="generateRandomChord">Generar acorde random</button>
      <button
          class="text-white bg-gray-800 hover:bg-gray-900 focus:outline-none focus:ring-4 focus:ring-gray-300 font-medium rounded-lg text-sm px-5 py-2.5 mr-2 mb-2 dark:bg-gray-800 dark:hover:bg-gray-700 dark:focus:ring-gray-700 dark:border-gray-700"
          @click="showChord"
      >Mostrar acorde</button>
      <button
          class="text-white bg-gray-800 hover:bg-gray-900 focus:outline-none focus:ring-4 focus:ring-gray-300 font-medium rounded-lg text-sm px-5 py-2.5 mr-2 mb-2 dark:bg-gray-800 dark:hover:bg-gray-700 dark:focus:ring-gray-700 dark:border-gray-700"
          @click="replayChord"
      >Volver a tocar el acorde</button>
    </div>
  </div>
</template>

<style scoped>
.pedalboard {
  width: 100vw;
  height: 100vh;
}
</style>
