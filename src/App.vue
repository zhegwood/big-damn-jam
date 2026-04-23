<script setup lang="ts">
import { ref } from 'vue'

const instruments = ref<string[]>(['drums', 'bass', 'guitar'])

const drums = ref<string[]>(['Bones', 'George', 'Mikey', 'Mason', 'V', 'Oso', 'Paul'])

const guitars = ref<string[]>(['Monty', 'Aro', 'Bill', 'Carey', 'Ryan', 'Chris'])

const bass = ref<string[]>(['Zach', 'Bill', 'Don', 'Matthias'])

const keys = ref<string[]>(['Aaron', 'Kevin'])

const horns = ref<string[]>(['Paul', 'Oren', 'Mont', 'Matthias'])

const notes = ref<string[]>(['A', 'Bb', 'B', 'C', 'C#', 'D', 'Eb', 'E', 'F', 'F#', 'G', 'Ab'])

// Working lists for randomization (reset from hard-coded lists)
const availableInstruments = ref<string[]>([...instruments.value])
const availableDrums = ref<string[]>([...drums.value])
const availableGuitars = ref<string[]>([...guitars.value])
const availableBass = ref<string[]>([...bass.value])
const availableKeys = ref<string[]>([...keys.value])
const availableHorns = ref<string[]>([...horns.value])
const availableNotes = ref<string[]>([...notes.value])

const drummer = ref<string | null>(null)
const guitarPlayer = ref<string | null>(null)
const guitarPlayer2 = ref<string | null>(null)
const bassPlayer = ref<string | null>(null)
const keysPlayer = ref<string | null>(null)
const hornPlayer = ref<string | null>(null)
const percPlayer = ref<string | null>(null)
const starter = ref<string | null>(null)
const key = ref<string | null>(null)
const hasSecondGuitar = ref(false)
const hasPercussion = ref(false)
const hasKeys = ref(false)
const hasHorns = ref(false)
const seconds = ref(0)
const interval = ref<ReturnType<typeof setInterval>>()
const selectedPlayers = ref<Set<string>>(new Set())

const randomNumber = (max: number) => {
  return Math.floor(Math.random() * max)
}

const pickAndRemove = (
  available: string[],
  original: string[],
  exclude?: Set<string>,
): string | null => {
  if (available.length === 0) {
    available.splice(0, available.length, ...original)
  }
  if (available.length === 0) {
    return null
  }

  // Filter out excluded values
  let filtered = available
  if (exclude && exclude.size > 0) {
    filtered = available.filter((item) => !exclude.has(item))
  }

  if (filtered.length === 0) {
    // If all are excluded, reset and try again
    filtered = available
  }

  const index = randomNumber(filtered.length)
  const value = filtered[index]

  // Remove from available list
  const availableIndex = available.indexOf(value)
  if (availableIndex > -1) {
    available.splice(availableIndex, 1)
  }

  return value
}

const hasThirtyPercent = () => {
  return Math.random() < 0.3
}

const hasFortyPercent = () => {
  return Math.random() < 0.4
}

const hasFiftyPercent = () => {
  return Math.random() < 0.5
}

const randomizeGuitar2 = () => {
  const picked = pickAndRemove(availableGuitars.value, guitars.value, selectedPlayers.value)
  if (picked) {
    guitarPlayer2.value = picked
    selectedPlayers.value.add(picked)
  }
}

const randomizePercussion = () => {
  const picked = pickAndRemove(availableDrums.value, drums.value, selectedPlayers.value)
  if (picked) {
    percPlayer.value = picked
    selectedPlayers.value.add(picked)
  }
}

const randomize = () => {
  clearInterval(interval.value)
  selectedPlayers.value = new Set()

  const drummer_picked = pickAndRemove(availableDrums.value, drums.value, selectedPlayers.value)
  if (drummer_picked) {
    drummer.value = drummer_picked
    selectedPlayers.value.add(drummer_picked)
    // Special case: if Paul is drummer, exclude Chris
    if (drummer_picked === 'Paul') {
      selectedPlayers.value.add('Chris')
    }
  }

  const guitar_picked = pickAndRemove(availableGuitars.value, guitars.value, selectedPlayers.value)
  if (guitar_picked) {
    guitarPlayer.value = guitar_picked
    selectedPlayers.value.add(guitar_picked)
    // Special case: if Chris is guitarist, exclude Paul from both drums and horns
    if (guitar_picked === 'Chris') {
      selectedPlayers.value.add('Paul')
      // If Paul was already picked as drummer, re-pick
      if (drummer.value === 'Paul') {
        drummer.value = pickAndRemove(availableDrums.value, drums.value, selectedPlayers.value)
        if (drummer.value) {
          selectedPlayers.value.add(drummer.value)
        }
      }
    }
  }

  const guitar2_picked = pickAndRemove(availableGuitars.value, guitars.value, selectedPlayers.value)
  if (guitar2_picked) {
    guitarPlayer2.value = guitar2_picked
    selectedPlayers.value.add(guitar2_picked)
  }

  const bass_picked = pickAndRemove(availableBass.value, bass.value, selectedPlayers.value)
  if (bass_picked) {
    bassPlayer.value = bass_picked
    selectedPlayers.value.add(bass_picked)
  }

  const keys_picked = pickAndRemove(availableKeys.value, keys.value, selectedPlayers.value)
  if (keys_picked) {
    keysPlayer.value = keys_picked
    selectedPlayers.value.add(keys_picked)
  }

  const horn_picked = pickAndRemove(availableHorns.value, horns.value, selectedPlayers.value)
  if (horn_picked) {
    hornPlayer.value = horn_picked
    selectedPlayers.value.add(horn_picked)
  }

  const perc_picked = pickAndRemove(availableDrums.value, drums.value, selectedPlayers.value)
  if (perc_picked) {
    percPlayer.value = perc_picked
    selectedPlayers.value.add(perc_picked)
  }

  key.value = pickAndRemove(availableNotes.value, notes.value)
  starter.value = pickAndRemove(availableInstruments.value, instruments.value)
  hasSecondGuitar.value = Boolean(hasFiftyPercent())
  hasPercussion.value = Boolean(hasFiftyPercent())
  hasKeys.value = Boolean(hasThirtyPercent())
  hasHorns.value = Boolean(hasFortyPercent())
}

const startCounter = () => {
  seconds.value = 5
  interval.value = setInterval(() => {
    if (seconds.value === 1) {
      randomize()
    }
    seconds.value -= 1
  }, 1000)
}
</script>

<template>
  <div class="flex flex-col gap-8 p-8">
    <span class="text-6xl text-center text-orange-600 font-weight-bold">The Big Damn Jam!</span>
    <div class="text-center">
      <button
        class="inline-block px-4 py-2 font-medium bg-orange-200 border border-orange-600 rounded"
        type="button"
        @click="startCounter"
      >
        Randomize
      </button>
    </div>
    <div class="flex flex-row gap-8">
      <div
        class="p-4 text-center border border-black border-orange-600 bg-orange-50 rounded-xl min-h-[326px]"
      >
        <h1 class="mb-4 text-4xl">The Players</h1>
        <div class="flex flex-row gap-8 flex-nowrap justify-self-center">
          <div>
            <h2 class="mb-2 text-2xl">Drums</h2>
            <p v-for="d in drums" :key="d" class="text-lg">{{ d }}</p>
          </div>
          <div>
            <h2 class="mb-2 text-2xl">Guitar</h2>
            <p v-for="g in guitars" :key="g" class="text-lg">{{ g }}</p>
          </div>
          <div>
            <h2 class="mb-2 text-2xl">Bass</h2>
            <p v-for="b in bass" :key="b" class="text-lg">{{ b }}</p>
          </div>
          <div>
            <h2 class="mb-2 text-2xl">Keyboard</h2>
            <p v-for="k in keys" :key="k" class="text-lg">{{ k }}</p>
          </div>
          <div>
            <h2 class="mb-2 text-2xl">Horn/Harp</h2>
            <p v-for="h in horns" :key="h" class="text-lg">{{ h }}</p>
          </div>
        </div>
      </div>
      <div class="flex-1">
        <div
          v-if="seconds > 0"
          class="flex items-center justify-center border border-orange-600 bg-orange-50 rounded-xl min-h-[326px]"
        >
          <span class="text-9xl">{{ seconds }}</span>
        </div>
        <div
          v-else-if="guitarPlayer"
          class="p-4 text-center border border-orange-600 bg-orange-50 rounded-xl min-h-[326px]"
        >
          <h1 class="mb-4 text-5xl">The Jam</h1>
          <div class="flex flex-row gap-8 mb-8 justify-self-center">
            <div>
              <h2 class="mb-2 text-4xl">Guitar</h2>
              <p class="text-2xl">{{ guitarPlayer }}</p>
            </div>
            <div>
              <h2 class="mb-2 text-4xl">Bass</h2>
              <p class="text-2xl">{{ bassPlayer }}</p>
            </div>
            <div>
              <h2 class="mb-2 text-4xl">Drums</h2>
              <p class="text-2xl">{{ drummer }}</p>
            </div>
            <div v-if="hasSecondGuitar">
              <h2 class="mb-2 text-4xl">Guitar 2</h2>
              <p class="text-2xl">{{ guitarPlayer2 }}</p>
              <button
                v-if="guitarPlayer === guitarPlayer2"
                class="px-2 mt-2 border border-black rounded-lg"
                type="button"
                @click="randomizeGuitar2"
              >
                Nope
              </button>
            </div>
            <div v-if="hasPercussion">
              <h2 class="mb-2 text-4xl">Percussion</h2>
              <p class="text-2xl">{{ percPlayer }}</p>
              <button
                v-if="drummer === percPlayer"
                class="px-2 mt-2 border border-black rounded-lg"
                type="button"
                @click="randomizePercussion"
              >
                Nope
              </button>
            </div>
            <div v-if="hasKeys">
              <h2 class="mb-2 text-4xl">Keys</h2>
              <p class="text-2xl">{{ keysPlayer }}</p>
            </div>
            <div v-if="hasHorns">
              <h2 class="mb-2 text-4xl">Sax</h2>
              <p class="text-2xl">{{ hornPlayer }}</p>
            </div>
          </div>
          <!--<p class="mb-8 text-4xl">Key: {{ key }} {{ majMin }}</p>-->
          <p class="mb-8 text-4xl">Key: {{ key }}</p>
          <p class="text-4xl">Who Starts: {{ starter }}</p>
        </div>
      </div>
    </div>
  </div>
</template>
