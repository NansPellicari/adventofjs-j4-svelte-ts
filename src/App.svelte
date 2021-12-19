<script lang="ts">
  import { onMount } from 'svelte'
  import { keys, SpecialKeysVal, SpecialKeysKeys } from './keys'
  import Button, { currentKey, standingKey } from './component/Button.svelte'

  let lastDate: Date | null = null
  let errors = 0
  let tries = 0
  let intervals: number[] = []

  $: interval = intervals.length > 0 ? intervals[intervals.length - 1] : 0
  $: average =
    intervals.length > 0
      ? intervals.reduce((p, c) => p + c) / intervals.length
      : 0

  let flatKeys = keys
    .flatMap((k) => k)
    .map((key: string) => {
      let dataKey = key.toUpperCase()
      const idx = SpecialKeysVal.indexOf(key)

      if (idx > -1) {
        dataKey = SpecialKeysKeys[idx]
      }
      return dataKey
    })

  onMount(() => {
    pickupKey()
  })

  const pickupKey = () => {
    const randomIndex = Math.floor(Math.random() * (flatKeys.length - 1))
    standingKey.set(flatKeys[randomIndex])
  }

  const setKey = function (event: KeyboardEvent) {
    currentKey.set(event.key.toUpperCase())
    if ($standingKey == $currentKey) {
      if (lastDate !== null) {
        intervals = [...intervals, new Date().getTime() - lastDate.getTime()]
        tries++
      } else {
        intervals = [0]
      }
      pickupKey()
      lastDate = new Date()
    } else {
      errors += 1
    }
  }
</script>

<svelte:window on:keydown|preventDefault={setKey} />

<main class="app">
  {#if interval > 0}
    <div class="score">
      <p>time response: {(interval / 1000).toFixed(2)}s</p>
      <p>average response: {Math.floor(average / 1000).toFixed(2)}s</p>
      <p class="error">errors: {errors}</p>
      <p>tries: {tries}</p>
    </div>
  {/if}
  <div class="keyboard">
    <h1>Eyes on the Screen</h1>
    {#each keys as row}
      <div class="row">
        {#each row as key}
          <Button {key} />
        {/each}
      </div>
    {/each}
  </div>
</main>
