<script setup lang="ts">
import { ref } from 'vue'

const instruments = ref<string[]>(['drums', 'bass', 'guitar'])

const drums = ref<string[]>(['Bones', 'George', 'Mikey', 'Mason', 'Kurt'])

const guitars = ref<string[]>(['Monty', 'Marcus', 'Nate', 'Carey', 'Ryan'])

const bass = ref<string[]>(['Zach', 'John', 'Don'])

const keys = ref<string[]>(['Aaron'])

const horns = ref<string[]>(['Paul', 'Aidan'])

const harp = ref<string[]>(['Oren', 'Marcus'])

const notes = ref<string[]>([
  'A',
  'A#/Bb',
  'B',
  'C',
  'C#/Db',
  'D',
  'D#/Eb',
  'E',
  'F',
  'F#/Gb',
  'G',
  'G#/Ab',
])

const drummer = ref<string | null>(null)
const guitarPlayer = ref<string | null>(null)
const guitarPlayer2 = ref<string | null>(null)
const bassPlayer = ref<string | null>(null)
const keysPlayer = ref<string | null>(null)
const hornPlayer = ref<string | null>(null)
const harpPlayer = ref<string | null>(null)
const percPlayer = ref<string | null>(null)
const starter = ref<string | null>(null)
const key = ref<string | null>(null)
const hasSecondGuitar = ref(false)
const hasPercussion = ref(false)
const hasKeys = ref(false)
const hasHorns = ref(false)
const hasHarp = ref(false)
const seconds = ref(0)
const interval = ref<ReturnType<typeof setInterval>>()

const randomNumber = (max: number) => {
  return Math.floor(Math.random() * max)
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
  guitarPlayer2.value = guitars.value[randomNumber(guitars.value.length)]
}

const randomizePercussion = () => {
  percPlayer.value = drums.value[randomNumber(guitars.value.length)]
}

const randomize = () => {
  clearInterval(interval.value)
  drummer.value = drums.value[randomNumber(drums.value.length)]
  guitarPlayer.value = guitars.value[randomNumber(guitars.value.length)]
  guitarPlayer2.value = guitars.value[randomNumber(guitars.value.length)]
  bassPlayer.value = bass.value[randomNumber(bass.value.length)]
  keysPlayer.value = keys.value[randomNumber(keys.value.length)]
  hornPlayer.value = horns.value[randomNumber(horns.value.length)]
  harpPlayer.value = harp.value[randomNumber(harp.value.length)]
  percPlayer.value = drums.value[randomNumber(drums.value.length)]
  key.value = notes.value[randomNumber(notes.value.length)]
  starter.value = instruments.value[randomNumber(instruments.value.length)]
  hasSecondGuitar.value = Boolean(hasFiftyPercent())
  hasPercussion.value = Boolean(hasFiftyPercent())
  hasKeys.value = Boolean(hasThirtyPercent())
  hasHorns.value = Boolean(hasFortyPercent())
  hasHarp.value = Boolean(hasFortyPercent())
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
    <div class="p-4 text-center border border-black border-orange-600 bg-orange-50 rounded-xl">
      <h1 class="mb-4 text-3xl">The Players</h1>
      <div class="flex flex-row gap-8 mb-8 flex-nowrap justify-self-center">
        <div>
          <h2 class="mb-2 text-xl">Drums</h2>
          <p v-for="d in drums" :key="d">{{ d }}</p>
        </div>
        <div>
          <h2 class="mb-2 text-xl">Guitar</h2>
          <p v-for="g in guitars" :key="g">{{ g }}</p>
        </div>
        <div>
          <h2 class="mb-2 text-xl">Bass</h2>
          <p v-for="b in bass" :key="b">{{ b }}</p>
        </div>
        <div>
          <h2 class="mb-2 text-xl">Keyboard</h2>
          <p v-for="k in keys" :key="k">{{ k }}</p>
        </div>
        <div>
          <h2 class="mb-2 text-xl">Sax</h2>
          <p v-for="h in horns" :key="h">{{ h }}</p>
        </div>
        <div>
          <h2 class="mb-2 text-xl">Harmonica</h2>
          <p v-for="h in harp" :key="h">{{ h }}</p>
        </div>
      </div>
    </div>
    <div class="text-center">
      <button
        class="inline-block px-4 py-2 font-medium bg-orange-200 border border-orange-600 rounded"
        type="button"
        @click="startCounter"
      >
        Randomize
      </button>
    </div>
    <div
      v-if="seconds > 0"
      class="p-4 text-center border border-orange-600 bg-orange-50 rounded-xl"
    >
      <span class="text-9xl">{{ seconds }}</span>
    </div>
    <div
      v-else-if="guitarPlayer"
      class="p-4 text-center border border-orange-600 bg-orange-50 rounded-xl"
    >
      <h1 class="mb-4 text-3xl">The Jam</h1>
      <div class="flex flex-row gap-8 mb-8 justify-self-center">
        <div>
          <h2 class="mb-2 text-xl">Guitar</h2>
          <p>{{ guitarPlayer }}</p>
        </div>
        <div>
          <h2 class="mb-2 text-xl">Bass</h2>
          <p>{{ bassPlayer }}</p>
        </div>
        <div>
          <h2 class="mb-2 text-xl">Drums</h2>
          <p>{{ drummer }}</p>
        </div>
        <div v-if="hasSecondGuitar">
          <h2 class="mb-2 text-xl">Guitar 2</h2>
          <p>{{ guitarPlayer2 }}</p>
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
          <h2 class="mb-2 text-xl">Percussion</h2>
          <p>{{ percPlayer }}</p>
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
          <h2 class="mb-2 text-xl">Keys</h2>
          <p>{{ keysPlayer }}</p>
        </div>
        <div v-if="hasHorns">
          <h2 class="mb-2 text-xl">Sax</h2>
          <p>{{ hornPlayer }}</p>
        </div>
        <div v-else-if="hasHarp">
          <h2 class="mb-2 text-xl">Harmonica</h2>
          <p>{{ harpPlayer }}</p>
        </div>
      </div>
      <!--<p class="mb-8 text-xl">Key: {{ key }} {{ majMin }}</p>-->
      <p class="mb-8 text-xl">Key: {{ key }}</p>
      <p class="text-xl">Who Starts: {{ starter }}</p>
    </div>
  </div>
</template>
