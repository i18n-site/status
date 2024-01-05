<template lang="pug">
+if li
  +else
    WAIT

</template>

<script lang="coffee">
> @~3/wait:WAIT
  @5-/alive/S.js > Li

+ li

onMount =>
  console.log 123, Li
  return
</script>

<style lang="stylus"></style>
