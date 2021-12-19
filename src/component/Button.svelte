<script context="module" lang="ts">
  import { writable } from 'svelte/store'
  export const currentKey = writable('')
  export const standingKey = writable('')
</script>

<script lang="ts">
  import { onMount } from 'svelte'
  import { SpecialKeysVal, SpecialKeysKeys } from '../keys'

  export let key: string

  $: isJiggle = $standingKey === dataKey

  let dataKey: string

  onMount((): void => {
    dataKey = key.toUpperCase()
    const idx = SpecialKeysVal.indexOf(key)

    if (idx > -1) {
      dataKey = SpecialKeysKeys[idx]
    }
  })
</script>

<button class="key" class:jiggle={isJiggle}>
  {key}
</button>
